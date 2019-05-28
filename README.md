# act_as_pingback

[![IHV](https://img.shields.io/badge/member%20of-Invisible%20Hat%20Ventures-blue.svg)](https://github.com/invisiblehats)

---

A rails integration for [pingback-ruby](https://github.com/democracy-news/pingback-ruby)

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'act_as_pingback', github: 'democracy-news/act_as_pingback'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install act_as_pingback

## Usage
You need to create a free account [here](#) before using this gem.
```ruby
Pingback.configure do |config|
  config.api_token = ENV['PINGBACK_TOKEN']
end
```

Install `act_as_pingback` in the models you want and enable which fields you want to include in the pingback:

```ruby
class Post < ApplicationRecord
  act_as_pingback fields: [
                    :title,
                    :body,
                    :url
                  ]
end
```

This would send a request to `pingback.democracy.news` with:

```ruby
{
  title: "Hello world",
  body: "<p>I created this using a tutorial from https://google.com and <a href=\"https://code.tutsplus.com/\">envato</a></p>",
  url: "https://myblog.com/posts/hello-world"
}
```

on `post#create` and `post#update` `post#delete` by default.

`url` by default will be `Rails.application.routes.url_helpers.url(self)`. To override this:
```ruby
class Post < ApplicationRecord
  act_as_pingback fields: [
                    :formatted_title,
                    :body,
                    :url
                  ],
                  callbacks: false,
                  on: [
                    :create
                  ],
                  async: true,
                  queue: 'cms'

    def url
      "https://myotherblog.com/posts/#{slug}"
    end

    def formatted_title
      "[POST] #{title}"
    end
end
```

Becomes:

```ruby
{
  title: "[POST] Hello world",
  body: "<p>I created this using a tutorial from https://google.com and <a href=\"https://code.tutsplus.com/\">envato</a></p>",
  url: "https://myotherblog.com/posts/hello-world"
}
```

### Options
Argument | Type | Default | Description
--|--|--|--
`fields` | `Array` (symbols) | `[]` | A list of the fields to include in the request. These would be any with links to process.
`callbacks` | `Boolean` | `true` | Trigger on active record callbacks? _(Only enabled for `ActiveRecord` objects)_
`on` | `Array` | `:create, :update, :delete` | Which lifecycle events should this library fire for
`async` | `Boolean` | `false` | Should this be run in the background?
`queue` | `String` | `default` | The queue name for this to run in _(Only supports `Sidekiq` right now)_

```ruby
# example
class Post < ApplicationRecord
  act_as_pingback fields: [
                    :title,
                    :body,
                    :url
                  ],
                  callbacks: false,
                  on: [
                    :create
                  ],
                  async: true,
                  queue: 'cms'

end
```


## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/democracy-news/act_as_pingback. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the ClassName project’s codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/democracy-news/act_as_pingback/blob/master/CODE_OF_CONDUCT.md).

## Development team
### People
- [Myk Klemme - @mklemme](https://github.com/mklemme) Lead developer and maintainer for this project.
### Partners
- [Invisible Hat Ventures - @invisiblehats](https://github.com/invisiblehats) A private startup development agency
- [Love.irish - @love-irish](https://github.com/love-irish) A lifestyle-as-a-service for the Irish language
- [League of Celts - @league-of-celts](https://github.com/league-of-celts) A non-profit focused on promoting Celtic languages
