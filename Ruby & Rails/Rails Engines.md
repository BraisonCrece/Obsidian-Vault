---
sticker: 1f685
banner: https://erepublic.brightspotcdn.com/dims4/default/589c04b/2147483647/strip/true/crop/1200x583+0+108/resize/1440x700!/quality/90/?url=http%3A%2F%2Ferepublic-brightspot.s3.amazonaws.com%2Fdc%2F4f%2F24c0496f6505cbc54cfbb2feb7e4%2Ffreight-rail-train.jpg
---
## What is Rails Engines? 🤔
Rails Engines are miniature applications that provide functionality to their host applications. A Rails application is actually just a "supercharged" engine, with the `Rails::Application` class inheriting a lot of its behavior from `Rails::Engine`. They share a common structure and can be thought of as almost the same thing, with subtle differences.

```ruby
module MyEngine
  class Engine < ::Rails::Engine
  end
end
```

## Name spacing `isolate_namespace` 🏷️
The `isolate_namespace` method is used within an engine to keep its controllers, models, routes, and other components separate from those of the host application. This helps to prevent naming conflicts.

```ruby
module MyEngine
  class Engine < ::Rails::Engine
    isolate_namespace MyEngine
  end
end
```

## How to manage migrations 📦
Migrations in Rails Engines are managed in a similar way to those in standard Rails applications. They are placed in the `db/migrate` directory of the engine. When the host application runs its migrations, the migrations of the engine are also run. You can also copy migrations from the engine into the host application using the following command:

```bash
rails {name_of_the_engine}:install:migrations
```

## Migrations scope 🌐
The migrations of a Rails Engine are scoped to the engine itself. This means that the tables created by the migrations are prefixed with the engine's name, preventing conflicts with the tables of the host application.

```ruby
class CreateMyEngineUsers < ActiveRecord::Migration[6.0]
  def change
    create_table :my_engine_users do |t|
      t.string :name
      t.timestamps
    end
  end
end
```

## Using a controller provided by the application (::ApplicationController) 🎮
Engines can use controllers provided by the host application. This is done by inheriting from `::ApplicationController`. This makes it easier to convert existing applications into engines.

```ruby
module MyEngine
  class MyController < ::ApplicationController
    def index
      # ...
    end
  end
end
```

## Setting config settings (mattr_accessor) ⚙️
The `mattr_accessor` method is used to set configuration settings for the engine. These settings can be accessed and modified from the host application, providing a way to customize the behavior of the engine.

```ruby
module MyEngine
  mattr_accessor :my_setting do
    'default value'
  end
end
```

## How to override 🔄
Functionality provided by a Rails Engine can be overridden in the host application. This allows the host application to customize the behavior of the engine to suit its specific needs. For example, if you want to override the `index` action of `MyController` in the engine, you can do so in the host application like this:

```ruby
MyEngine::MyController.class_eval do
  def index
    # new implementation...
  end
end
```

## Routing helpers 🚦
Routing helpers in Rails Engines work similarly to those in standard Rails applications, but they are scoped to the engine. This means that the routing helpers can be used without causing conflicts with the routes of the host application.

```ruby
MyEngine::Engine.routes.draw do
  resources :users
end
```

In the host application, you can use the engine's routes like this:

```ruby
link_to 'Engine User', my_engine.users_path
```