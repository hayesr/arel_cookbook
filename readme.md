# Eric's Arel Guide

Status: Super Alpha, everything is a rough draft

## Table of Contents

- [Cookbook](cookbook/readme.md)
- [How Arel Works](foundation.md)
- [SQL Intro & Resources](sql.md)

## What is Arel?

(Explain the history)

### Description (History)

Arel (sometimes stylized ARel) originally stood for "A Relational Algebra", over its history the original readme described it in different ways:

- 1.0: A Relational Algebra
- 3.0: A SQL AST manager for Ruby
- 7.1: "Arel Really Exasperates Logicians"

By 6.0, the team behind Arel stopped using the phrase "Relational Algebra" and described it a SQL AST manager.

The original readme included this:

> It is intended to be a framework framework; that is, you can build your own ORM with it, focusing on innovative object and collection modeling as opposed to database compatibility and query generation.

Arel (mostly) abstracts the SQL nuances between different databases, and it uses an AST with the visitor pattern to achieve that. In the end Arel is about building strings of SQL.

### Arel Is Private API

At the time of this writing, Arel is considered _private API_, which doesn't mean that you are unable to use it (of course) but it does mean using it is your responsibility.

For example, say the team wanted to make a big change the way `has_many` (public API) worked. You could expect discussion about it ahead of time, blog announcements, deprecation warnings, work-arounds, etc. On the other hand, with Arel, the way things work can (and will) change between versions without any warning. (This is not a statement about the quality of Arel which is very well tested.)

Therefore it falls to the developer that uses Arel to ensure that their code produces the SQL they expect.

Mostly things stay pretty stable. My team has carried Arel code from Rails 4.2. Over that time we've seen things like whitespaces changes, or changes to how `between` works. I did not notice changes in our upgrade from Rails 6.2 to 7.

In practice if you have Arel code, and you want it to work after a Rails upgrade, you need to [test the SQL output](cookbook/testing.md).

## To Do

- [ ] Intro/History
- [ ] Private API
- [ ] Foundational Concepts
  - [ ] SQL Primer
  - [ ] AST/Nodes
  - [ ] Glossary
- [ ] Cookbook
  - [ ] Testing
  - [ ] Execution
  - [ ] Handling common issues
  - [ ] Simple select
  - [ ] Comparison
  - [ ] Working with JSON
  - [ ] CTEs
  - [ ] Aggregate Functions
  - [ ] Window Functions
    - [ ] Window Node
    - [ ] Over
    - [ ] Frames
  - [ ] Math
    - [ ] Simple
    - [ ] Percents & percent changes
  - [ ] FILTER
  - [ ] Misc
    - [ ] Not
    - [ ] Grouping
    - [ ] Build quoted
    - [ ] CASE statements
  - [ ] Inserts
  - [ ] Updates
  - [ ] Deletes
  - [ ] How to hunt
    - [ ] Reading the source
    - [ ] Named Function
    - [ ] Infix Operation
    - [ ] Unary Operation
