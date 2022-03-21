# Executing SQL

## Executing a select (expecting rows)

```ruby
ApplicationRecord.connection.execute(arel.to_sql)
```

## Getting a single value
Usually execution returns an array of rows, this is a shortcut that returns the
first value of the first row.

```ruby
ApplicationRecord.connection.query_value(sql)
```

## Handling replicas
```ruby
begin
  # Try the reading replica first
  ApplicationRecord.connected_to(role: :reading) do
    ApplicationRecord.connection.query_value(arel.to_sql)
  end
rescue ActiveRecord::ConnectionNotEstablished => err
  # Try the primary
  ApplicationRecord.connection.query_value(arel.to_sql)
end
```

## Executing commands
While execute alone works, this will return the number of rows affected by the SQL command (for certain commands)

```ruby
ApplicationRecord.connection.execute(to_sql).cmd_tuples
```
