---
title: High-level Rules
layout: gem-single
---

It is often not enough to define simple type-checking rules and in addition to those you need to be able to specify higher-level rules that simply rely on other rules. This can be achieved using `rule` interface which can access already defined rules for specific keys.

In example let's say we have a schema with 3 keys, `:barcode`, `:job_number` and `:sample_number` and we need to make sure that when barcode is provided then both job and sample numbers cannot be provided. The low-level checks need to make sure that individual values have correct state and on top of that we can define our high-level rule:

``` ruby
schema = Dry::Validation.Schema do
  required(:barcode).maybe(:str?)

  required(:job_number).maybe(:int?)

  required(:sample_number).maybe(:int?)

  rule(barcode_only: [:barcode, :job_number, :sample_number]) do |barcode, job_num, sample_num|
    barcode.filled? & (job_num.none? & sample_num.none?)
  end
end
```

This way we have validations for individual keys and the high-level `:barcode_only` rule which says "barcode can be filled only if both job_number and sample_number are empty".

## Rules Depending On Values From Other Rules

Similar to rules that depend on results from other rules, you can define high-level rules that need to apply additional predicates to values provided by other rules. In example, let's say we want to validate presence of an `email` address but only when `login` value is set to `true`:

``` ruby
schema = Dry::Validation.Schema do
  required(:login).filled(:bool?)
  required(:email).maybe(:str?)

  rule(email_presence: [:login, :email]) do |login, email|
    login.true?.then(email.filled?)
  end
end
```

This translates to "login set to true implies that email must be present".

We can also easily specify a rule for the absence of an email:

``` ruby
schema = Dry::Validation.Schema do
  required(:login).filled(:bool?)
  required(:email).maybe(:str?)

  rule(email_absence: [:login, :email]) do |login, email|
    login.false?.then(email.none?)
  end
end
```

Notice that you must add `:email_absence` message to configuration if you want to have the error converted to a message.
