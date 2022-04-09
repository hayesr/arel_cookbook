# Building Mathematical Expressions With Arel

Performing calculations with Arel is a simple matter of wrapping left and right pairs in the node that represents the operation you wish to perform.

Let's start with simple addition:

```ruby
Arel::Nodes::Addition.new(1, 2).to_sql
# => "1 + 2"
```

Here we pass the integers 1 and 2 to the `Addition` node. Under the hood, `Addition` is a child of the `InfixOperation`. Putting the `+` sign between our numbers isn't a "prefix" or a "suffix", it's an _infix_ operation. `InfixOperation` is used extensively, because a lot of the expressions we want to build in SQL involve combining a left and a right with something in the middle.

What about a more complicated example? Let's calculate the percent change between two values:

```ruby
percent_change = ((recent_value / prior_value) - 1) * 100
```

Expressing this idea in Arel might look like:

```ruby
recent_value = 456
prior_value  = 123

Arel::Nodes::Multiplication.new(
  Arel::Nodes::Grouping.new(
    Arel::Nodes::Subtraction.new(
      Arel::Nodes::Grouping.new(
        Arel::Nodes::Division.new(recent_value, prior_value)
      ),
      1
    )
  ),
  100
)

# .to_sql => "((456 / 123) - 1) * 100"
```

This is pretty cumbersome code, but it gets even worse. If we try to use this in dynamic code where the prior code can be zero, we can end up with a _division by zero_ error. So we need to guard our values with `NULLIF` and `COALESCE`, then we have:

```ruby
Arel::Nodes::NamedFunction.new('COALESCE', [
  Arel::Nodes::Multiplication.new(
    Arel::Nodes::Grouping.new(
      Arel::Nodes::Subtraction.new(
        Arel::Nodes::Grouping.new(
          Arel::Nodes::Division.new(
            recent_value,
            Arel::Nodes::NamedFunction.new('NULLIF', [prior_value, 0])
          )
        ),
        1
      )
    ),
    100
  ),
  0
])

# .to_sql => "COALESCE(((456 / NULLIF(123, 0)) - 1) * 100, 0)"
```

That's pretty tough to make sense of. Let's clean things up. Here's a way to break this up into methods that we can mixin to query code as needed:

```ruby
module ConditionalExpressions
  def coalesce(column, value = 0)
    Arel::Nodes::NamedFunction.new('COALESCE', [column, value])
  end

  def nullif(expression, compare = 0)
    Arel::Nodes::NamedFunction.new('nullif', [expression, compare])
  end
end

module MathFunctions
  def divide(left, right)
    Arel::Nodes::Division.new(left, right)
  end

  def multiply(left, right)
    Arel::Nodes::Multiplication.new(left, right)
  end

  def add(column1, column2)
    Arel::Nodes::Addition.new(column1, column2)
  end

  def subtract(column1, column2)
    Arel::Nodes::Subtraction.new(column1, column2)
  end

  # COALESCE(((recent / nullif(prior, 0)) - 1) * 100, 0)
  def percent_change(recent, prior)
    divi = divide(recent, nullif(prior))
    subt = subtract(grouping(divi), 1)
    mult = multiply(grouping(subt), 100)

    coalesce(mult)
  end
end
```

Then, we can include these modules into a query class. (Imagine we had a table or a CTE with recent_value and prior_value columns)

```ruby
class PercentChangeQuery
  include ConditionalExpressions
  include MathFunctions

  def query
    my_table.project(percent_change(recent_value, prior_value))
  end

  def to_sql
    query.to_sql
  end
end
```

Calling `PercentChangeQuery.new.to_sql` would yield:

```sql
SELECT COALESCE((("my_table"."recent_value" / nullif("my_table"."prior_value", 0)) - 1) * 100, 0)
FROM "my_table"
```

## Conclusion

The trick to building a complex math expression is figuring out the left-right pairs and nesting everything correctly.
Defining methods to hide the complexity makes the code easier to read.
