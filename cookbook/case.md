# CASE Statements

SQL CASE statements can be constructed with the `Case` node. Instances of
`Arel::Nodes::Case` have `when`, `then`, and `else` methods to build the conditional.

This block style using `tap` aligns with the SQL style:

```ruby
Arel::Nodes::Case.new.tap do |c|
  c.when(tbl[:name].eq('foo')).then(1)
  c.when(tbl[:name].eq('bar')).then(2)
  c.else(0)
end
```

```SQL
CASE
  WHEN "tbl"."name" == 'foo' THEN 1
  WHEN "tbl"."name" == 'bar' THEN 2
  ELSE 0
END
```

Arel CASE statements become more interesting with loops:

```ruby
attr_map = {
  period1: Date.new(2020, 3, 1)..Date.new(2021, 2, 28),
  period2: Date.new(2021, 3, 1)..Date.new(2022, 2, 28)
}

Arel::Nodes::Case.tap do |c|
  attr_map.each do |k, v|
    c.when(tbl[:ordered_on].between(v)).then(k)
  end

  c.else('out-of-range')
end
```

[Original Arel PR](https://github.com/rails/arel/pull/400)
