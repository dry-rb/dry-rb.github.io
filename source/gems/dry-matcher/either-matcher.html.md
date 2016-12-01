---
title: Either matcher
layout: gem-single
---

dry-matcher provides a ready-to-use `EitherMatcher` for working with `Either` or `Try` monads from [dry-monads](/gems/dry-monads) or any other compatible gems.

```ruby
require "dry-monads"
require "dry/matcher/either_matcher"

value = Dry::Monads::Either::Right.new("success!")

result = Dry::Matcher::EitherMatcher.(value) do |m|
  m.success do |v|
    puts "Yay: #{v}"
  end

  m.failure do |v|
    puts "Boo: #{v}"
  end
end

result # => "Yay: success!"
```

