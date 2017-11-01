---
title: Injecting dependencies
layout: gem-single
name: dry-view
---

Most real view controllers will need access to other parts of your application to prepare data for the view. Since view controllers follow the "functional object" pattern (local state for config and collaborators only, with any variable data passed to `#call`), it's easy to use dependency injection to make your application's objects available to your view controllers.

To setting up the injection manually:

```ruby
class UsersIndex < Dry::View::Controller
  attr_reader :user_repo

  def initialize(user_repo:)
    super()

    @user_repo = user_repo
  end

  expose :users do
    user_repo.listing
  end
end
```

Or if your app uses [dry-system](/gems/dry-system) or [dry-auto_inject](/gems/dry-auto_inject), this is even less work:

```ruby
# Require the injector for your app's container
require "my_app/import"

class UsersIndex < Dry::View::Controller
  include MyApp::Import["user_repo"]

  expose :users do
    user_repo.listing
  end
end
```
