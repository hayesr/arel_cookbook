# Eric's Arel Cookbook

## What Is Arel?

Arel (mostly) abstracts the SQL nuances between different databases, and it uses an AST with the visitor pattern to achieve that. In the end, Arel is about producing strings of SQL.

### History

Arel (sometimes stylized ARel) originally stood for "A Relational Algebra", over its history the original readme described it in different ways:

- 1.0: A Relational Algebra
- 3.0: A SQL AST manager for Ruby
- 7.1: "Arel Really Exasperates Logicians"

By 6.0, the team behind Arel seem to have stopped using the phrase "Relational Algebra" and described it a SQL AST manager.

The original readme included this description:

> It is intended to be a framework framework; that is, you can build your own ORM with it, focusing on innovative object and collection modeling as opposed to database compatibility and query generation.


## Why Use Arel?

- Composibility
- Handling names

## Your Responsibility

At the time of this writing, Arel is considered _private API_, which doesn't mean that you are unable to use it (of course) but it does mean using it is your responsibility.

For example, say the team wanted to make a big change the way `has_many` (public API) worked. You could expect discussion about it ahead of time, blog announcements, deprecation warnings, work-arounds, etc. On the other hand, with Arel, the way things work can (and will) change between versions without any warning. (This is not a statement about the quality of Arel which is very well tested.)

Therefore it falls to the developer that uses Arel to ensure that their code produces the SQL they expect.

Mostly things stay pretty stable. My team has carried Arel code from Rails 4.2. Over that time we've seen things like whitespaces changes, or changes to how `between` works. I did not notice changes in our upgrade from Rails 6.2 to 7.

In practice if you have Arel code, and you want it to work after a Rails upgrade, you need to [test the SQL output](cookbook/testing.md).

## Topics
*to be organized*

- [Executing Arel SQL](execution.md)
- A simple select query
- Comparisons
- Joins
- Working with JSON
- [Math](math.md)
- CTEs
- Aggregate Functions
- Window Functions
- [CASE Statements](case.md)
- Misc tools
- Inserts
- Updates
- Deletes

### Arel Source Survey
[TODO] A guided pass through the source code

### Testing
[TODO] How to test the output strings and check correctness with Postgres

### Troubleshooting
[TODO] Common errors and how fix them

## SQL Resources
[TODO] [SQL Intro & Resources](sql.md)

## Earlier Arel Articles
[TODO] Collection of older blog posts on Arel
