---
title: Introduction
description: Business transaction DSL
layout: gem-single
order: 7
type: gem
name: dry-transaction
sections:
  - basic-usage
  - step-notifications
  - step-adapters
  - custom-step-adapters
  - modifying-transactions
---

`dry-transaction` is a business transaction DSL. It provides a simple way to define a complex business transaction that includes processing by many different objects. It makes error handling a primary concern by using a “[Railway Oriented Programming](http://fsharpforfunandprofit.com/rop/)” approach for capturing and returning errors from any step in the transaction.

`dry-transaction` is based on the following ideas:

* A business transaction is a series of operations where any can fail and stop the processing.
* A business transaction resolves its dependencies using an external container object and it doesn’t know any details about the individual operation objects except their identifiers.
* A business transaction can describe its steps on an abstract level without being coupled to any details about how individual operations work.
* A business transaction doesn’t have any state.
* Each operation shouldn’t accumulate state, instead it should receive an input and return an output without causing any side-effects.
* The only interface of a an operation is `#call(input)`.
* Each operation provides a meaningful piece of functionality and can be reused.
* Errors in any operation can be easily caught and handled as part of the normal application flow.

## Why?

Requiring a business transaction’s steps to be independent operations directly addressable via a container means that they can be tested in isolation and easily reused throughout your application. The business transaction can then become a simple series of declarative steps, which ensures that it’s easy to understand at a glance.

The output of each step is a [dry-monads](https://github.com/dry-rb/dry-monads) `Either` object (`Right` for success or `Left` for failure). This allows the steps to be chained together and ensures that processing stops in the case of a failure. Returning an `Either` from the overall transaction also allows for error handling to remain a primary concern without it getting in the way of tidy, straightforward operation logic.

## Links

View the [full API documentation](http://www.rubydoc.info/github/dry-rb/dry-transaction) on RubyDoc.info.

## Credits

`dry-transaction` is developed and maintained by [Icelab](http://icelab.com.au/).

`dry-transaction`’s error handling is based on Scott Wlaschin’s [Railway Oriented Programming](http://fsharpforfunandprofit.com/rop/), found via Zohaib Rauf’s [Railway Oriented Programming in Elixir](http://zohaib.me/railway-programming-pattern-in-elixir/) blog post. dry-transaction’s behavior as a business transaction library draws heavy inspiration from Piotr Solnica’s [Transflow](http://github.com/solnic/transflow) and Gilbert B Garza’s [Solid Use Case](https://github.com/mindeavor/solid_use_case). Thank you all!
