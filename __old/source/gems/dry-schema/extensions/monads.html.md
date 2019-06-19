---
title: Monads
layout: gem-single
name: dry-schema
---

This extension add a new method (`#to_monad`) to validation result.

```ruby
Dry::Schema.load_extensions(:monads)

schema = Dry::Schema.Params { required(:name).filled(:str?, size?: 2..4) }
schema.call(name: 'Jane').to_monad # => Dry::Monads::Success({ name: 'Jane' })
schema.call(name: '').to_monad     # => Dry::Monads::Failure(name: ['name must be filled', 'name length must be within 2 - 4'])
```

This can be useful when used with `dry-monads` and the [`do` notation](/gems/dry-monads/do-notation/).
