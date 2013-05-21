# Paperclip Dropbox

This gem extends [Paperclip](https://github.com/thoughtbot/paperclip) with
[Dropbox](https://www.dropbox.com) storage.

## Setup

```ruby
gem "paperclip-dropbox", ">= 1"
```

Example:

```ruby
class User < ActiveRecord::Base
  has_attached_file :avatar,
    :storage => :dropbox,
    :dropbox_credentials => Rails.root.join("config/dropbox.yml"),
    :dropbox_options => {...}
end
```

Your `config/dropbox.yml`:

```yaml
app_key: "..."
app_secret: "..."
access_token: "..."
access_token_secret: "..."
user_id: "..."
access_type: "..."
```

In order to fill these in, you must [create a Dropbox app](https://www.dropbox.com/developers/apps)
and authorize it. There are two types of Dropbox apps: **App folder** or **Full Dropbox**. You can read
about the differences and gotchas in [this wiki](https://github.com/janko-m/paperclip-dropbox/wiki/Access-types).

After you have created an app, you will be given the "App key" and "App secret". Provide
these and the access type to the authorization Rake task:

```sh
$ rake dropbox:authorize APP_KEY=your_app_key APP_SECRET=your_app_secret ACCESS_TYPE=dropbox|app_folder
```

For non-Rails projects, you must require this task in your `Rakefile`:

```ruby
# Rakefile
load "paperclip/dropbox/tasks.rake"
```

Follow the instructions, and it will authorize the Dropbox app and output the rest of the credentials.
Then you can fill in the rest of `config/dropbox.yml`.

And that's it. Everything should work now :)

### The `:dropbox_credentials` option

```ruby
:dropbox_credentials => Rails.root.join("config/dropbox.yml")
# or
:dropbox_credentials => {app_key: "foo", app_secret: "bar", ...}
```

For the list of required credentials, take a look at the `config/dropbox.yml`
above.

The YAML file supports ERB:

```erb
app_key: <%= ENV["DROPBOX_APP_KEY"] %>
```

And it supports environment nesting (just like `database.yml`):

```yaml
development:
  app_key: "..."
  ...
production:
  app_key: "..."
  ...
```

### The `:dropbox_options` option

This is a hash containing any of the following options:

- `:environment` – String, the environment name to use for selecting namespaced
  credentials in a non-Rails app

For example:

```ruby
class User < ActiveRecord::Base
  has_attached_file :avatar,
    :storage => :dropbox,
    :dropbox_credentials => Rails.root.join("config/dropbox.yml"),
    :dropbox_options => {environment: ENV["RACK_ENV"]}
end
```

In Rails you don't need to specify it.

### The `:path` option

To change the path of the file, use Paperclip's `:path` option:

```ruby
:path => ":style/:id_:filename"  # Defaults to ":filename"
```

In "Full Dropbox" mode it will automatically be searched in the `Public` folder,
so there is not need to specify it.

### URL options

If you want to force the browser to always download the file, you can use
the `:download` option.

```ruby
user.avatar.url(:download => true)
```

### Check if the file exists

You can easily check if the file exists on Dropbox:

```ruby
user.avatar.exists?
# user.avatar.exists?(style)
```

## License

[MIT License](LICENSE)
