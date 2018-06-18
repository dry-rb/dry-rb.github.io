---
title: Introduction
description: Common monads for Ruby
layout: gem-single
type: gem
name: dry-monads
sections:
  - maybe
  - result
  - try
  - list
  - case-equality
  - task
  - do-notation
  - validated
---

`dry-monads` is a set of common monads for Ruby.
Monads provide an elegant way of handling errors, exceptions and chaining functions so that the code is much more understandable and has all the error handling, without all the `if`s and `else`s. The gem was inspired by [Kleisli](https://github.com/txus/kleisli) gem.

What is a monad, anyway? Simply, [a monoid in the category of endofunctors](https://stackoverflow.com/questions/3870088/a-monad-is-just-a-monoid-in-the-category-of-endofunctors-whats-the-proble%E2%85%BF). The term comes from Category Theory and there are beliefs monads are tough to understand or explain. It's hard to say why people think so because you certainly don't need to know Category Theory for using them, just like you don't need it for, say, using functions.

Moreover, the best way to develop intuition about monads is looking at examples rather than learning theories.

## How to use it?

Let's say you have code like this

```ruby
user = User.find(params[:id])

if user
  address = user.address
end

if address
  city = address.city
end

if city
  state = city.state
end

if state
  state_name = state.name
end

user_state = state_name || "No state"
```

Writing code in this style is tedious and error-prone. There were created several "cutting-corners" means to work around this issue. The first is ActiveSupport's `.try` which is a plain global monkey patch on `NilClass` and `Object`. Another solution is using the Safe Navigation Operator `&.` introduced in Ruby 2.3 which is a bit better because this is a language feature rather than an opinionated runtime environment pollution. However, some people think these solutions are hacks and the problem reveals a missing abstraction. What kind of abstraction?

When all objects from the chain of objects are there we could have this instead:

```ruby
state_name = User.find(params[:id]).address.city.state.name
user_state = state_name || "No state"
```

By using the `Maybe` monad you can preserve the structure of this code at a cost of introducing a notion of `nil`-able result:

```ruby
state_name = Maybe(User.find(params[:id])).fmap(&:address).fmap(&:city).fmap(&:state).fmap(&:name)
user_state = state_name.value_or("No state")
```

`Maybe(...)` wraps the first value and returns a monadic value which either can be a `Some(user)` or `None` if `user` is `nil`. `fmap(&:address)` transforms `Some(user)` to `Some(address)` but leaves `None` intact. To get the final value you can use `value_or` which is a safe way to unwrap a `nil`-able value. In other words, once you've used `Maybe` you _cannot_ hit `nil` with a missing method. This is remarkable because even `&.` doesn't save you from omitting `|| "No state"` at the end of the computation. Basically, that's what they call "Type Safety".

A more expanded example is based on _composing_ different monadic values. Suppose, we have a user and address, both can be `nil`, and we want to associate the address with the user:

```ruby
user = User.find(params[:user_id)
address = Address.find(params[:address_id)

if user && address
  user.update(address_id: address.id)
end
```

Again, this implies direct work with `nil`-able values which may end up with errors. A monad-way would be using another method, `bind`:

```ruby
maybe_user = Maybe(User.find(params[:user_id))

maybe_user.bind do |user|
  maybe_address = Maybe(Address.find(params[:address_id))

  maybe_address.bind do |address|
    user.update(address_id: address.id)
  end
end
```

One can say this code is opaque compared to the previous example but keep in mind that in _real code_ it often happens to call methods returning `Maybe` values. In this case, it might look like this:

```ruby
find_user(params[:user_id]).bind do |user|
  find_address(params[:address_id]).bind do |address|
    user.update(address_id: address.id)
  end
end
```

Another widely spread monad is `Result` (also known as `Either`) that serves a similar purpose. A notable downside of `Maybe` is plain `None` which carries no information about where this value was produced. `Result` solves exactly this problem by having two constructors for `Success` and `Failure` cases:

```ruby
def find_user(user_id)
  user = User.find(user_id)

  if user
    Success(user)
  else
    Failure(:user_not_found)
  end
end

def find_address(address_id)
  address = Address.find(address_id)

  if address
    Success(address)
  else
    Failure(:address_not_found)
  end
end
```

You can compose `find_user` and `find_address` with `bind`:

```ruby
find_user(params[:user_id]).bind do |user|
  find_address(params[:address_id]).bind |address|
    Success(user.update(address_id: address.id))
  end
end
```

The inner block can be simplified with `fmap`:

```ruby
find_user(params[:user_id]).bind do |user|
  find_address(params[:address_id]).fmap |address|
    user.update(address_id: address.id)
  end
end
```

The result of this piece of code can be one of `Success(user)`, `Failure(:user_not_found)`, or `Failure(:address_not_found)`. This style of programming called "Railway Oriented Programming" and can check out [dry-transaction](/gems/dry-transaction) and watch a [nice video](https://fsharpforfunandprofit.com/rop/) on the subject. Also, see [dry-matcher](/gems/dry-matcher/) for an example of how to use monads for controlling the flow of code with a result.

## A word of warning

If you're new to monads don't over-use them. You can use [`dry-transaction`](/gems/dry-transaction) as a robust wrapper that utilizes the `Result` monad, it's a good start for diving in. Remember that monads are not a first-class concept in Ruby and with writing safer code you may end up with way too complex one which is not great either, so use them judiciously.

In any case, if you're interested in functional programming in general consider learning other languages such as Haskell, Scala, OCaml, this will make you a better programmer no matter what programming language you use on a daily basis. And if not earlier then maybe after that `dry-monads` will become another instrument in your Ruby toolbox :)

## Credits

`dry-monads` is inspired by Josep M. Bach’s [Kleisli](https://github.com/txus/kleisli) gem and its usage by [`dry-transactions`](http://dry-rb.org/gems/dry-transaction/) and [`dry-types`](http://dry-rb.org/gems/dry-types/).
