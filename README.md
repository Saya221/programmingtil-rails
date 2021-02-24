# README

This README would normally document whatever steps are necessary to get the
application up and running.

Things you may want to cover:

* Ruby version

* System dependencies

* Configuration

* Database creation

* Database initialization

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions

* ...

# COMMANDS and things

## EPISODE 4

Gemfile
```ruby
  # Annotate models and more
  # https://github.com/ctran/annotate_models
  gem 'annotate'
```

```
bundle install
```

auto_annotate_models.rake
```ruby
# NOTE: only doing this in development as some production environments (Heroku)
# NOTE: are sensitive to local FS writes, and besides -- it's just not proper
# NOTE: to have a dev-mode tool do its thing in production.
if Rails.env.development?
  require 'annotate'
  task :set_annotation_options do
    # You can override any of these by setting an environment variable of the
    # same name.
    Annotate.set_defaults(
      'active_admin'                => 'false',
      'additional_file_patterns'    => [],
      'routes'                      => 'false',
      'models'                      => 'true',
      'position_in_routes'          => 'before',
      'position_in_class'           => 'before',
      'position_in_test'            => 'before',
      'position_in_fixture'         => 'before',
      'position_in_factory'         => 'before',
      'position_in_serializer'      => 'before',
      'show_foreign_keys'           => 'true',
      'show_complete_foreign_keys'  => 'false',
      'show_indexes'                => 'true',
      'simple_indexes'              => 'false',
      'model_dir'                   => 'app/models',
      'root_dir'                    => '',
      'include_version'             => 'false',
      'require'                     => '',
      'exclude_tests'               => 'false',
      'exclude_fixtures'            => 'true',
      'exclude_factories'           => 'true',
      'exclude_serializers'         => 'true',
      'exclude_scaffolds'           => 'true',
      'exclude_controllers'         => 'true',
      'exclude_helpers'             => 'true',
      'exclude_sti_subclasses'      => 'false',
      'ignore_model_sub_dir'        => 'false',
      'ignore_columns'              => nil,
      'ignore_routes'               => nil,
      'ignore_unknown_models'       => 'false',
      'hide_limit_column_types'     => 'integer,bigint,boolean',
      'hide_default_column_types'   => 'json,jsonb,hstore',
      'skip_on_db_migrate'          => 'false',
      'format_bare'                 => 'true',
      'format_rdoc'                 => 'false',
      'format_yard'                 => 'false',
      'format_markdown'             => 'false',
      'sort'                        => 'false',
      'force'                       => 'false',
      'frozen'                      => 'false',
      'classified_sort'             => 'false',
      'trace'                       => 'false',
      'wrapper_open'                => nil,
      'wrapper_close'               => nil,
      'with_comment'                => 'true'
    )
  end

  Annotate.load_tasks
end
```

```
rake db:migrate
```

## EPISODE 3

devise.rb
```ruby
  config.jwt do |jwt|
    jwt.secret = ENV['DEVISE_JWT_SECRET_KEY']
    jwt.dispatch_requests = [
      ['POST', %r{^/signin$}],
    ]
    jwt.revocation_requests = [
      ['DELETE', %r{^/signout$}]
    ]
    jwt.expiration_time = 14.days.to_i
    # Use default aud_header
    jwt.aud_header = 'JWT_AUD'
  end
```

user.rb
```ruby
  devise :database_authenticatable,
    :confirmable,
    :registerable,
    :recoverable,
    :rememberable,
    :validatable,
    :jwt_authenticatable,
    jwt_revocation_strategy: self
```

```bash
rails g migration createAllowlistedJwts
```

create_allowlisted_jwts.rb
```ruby
  def change
    create_table :allowlisted_jwts do |t|
      t.references :users, foreign_key: { on_delete: :cascade }, null: false
      t.string :jti, null: false
      t.string :aud, null: false
      t.datetime :exp, null: false
      t.string :remote_ip
      t.string :os_data
      t.string :browser_data
      t.string :device_data
      t.timestamps null: false
    end

    add_index :allowlisted_jwts, :jti, unique: true
  end
```

models/allowlisted_jwt.rb
```ruby
class AllowlistedJwt < ApplicationRecord
end
```

```
rake db:migrate
```



## EPISODE 2:

  database.yml > host: 127.0.0.1 -> (needed because WSL)

Gemfile
```ruby
gem 'devise', github: 'heartcombo/devise'
gem 'devise-jwt'
gem 'rack-cors'
```

```
rake db:create
```

```
rails generate devise:install
rails generate devise user
rails db:migrate
```

## EPISODE 1:
```
rails new programmingtil-rails-1 --api --database=postgresql
```
