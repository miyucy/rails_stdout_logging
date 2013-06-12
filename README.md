# Rails Stdout Logging

Rails gem to configure your app to log to standard out.

Supports:

- Rails 3
- Rails 4


## Install

In your `Gemfile` add:

```
gem 'rails_stdout_logging'
```

Then run

```
$ bundle install`
```

You also need the `rails_serve_static_assets` gem. You can get both of them together by installing `rails_on_heroku` gem.

## Why is this needed?

In the default Rails development environment assets are served through a middleware called [sprockets](https://github.com/sstephenson/sprockets). In production however most non-heroku Rails deployments will put their ruby server behind reverse HTTP proxy server such as Nginx which can load balance their sites and can serve static files directly. When Nginx sees a request for an asset such as `/assets/rails.png` it will grab it from disk at `/public/assets/rails.png` and serve it. The Rails server will never even sees the request.

On Heroku, Nginx is not needed to run your application. Our [routing layer](https://devcenter.heroku.com/articles/http-routing) handles load balancing while you scale out horizontally. The caching behavior of Nginx is not needed if your application is serving static assets through an [edge caching CDN](https://en.wikipedia.org/wiki/Content_delivery_network).

By default Rails4 will return a 404 if an asset is not handled via an external proxy such as Nginx. While this default behavior will help you debug your Nginx configuration, it makes a default Rails app with assets unusable on Heroku. To fix this we've released a gem `rails_serve_static_assets`.

This gem, `rails_serve_static_assets`, enables your Rails server to deliver your assets instead of returning a 404. You can use this to populate an edge cache CDN, or serve files directly from your web app. This gives your app total control and allows you to do things like redirects, or setting headers in your Ruby code. To enable this behavior in your app we only need to set this one configuration option:

```
config.serve_static_assets = true
```

You don't need to set this option since this that is what this gem does. Now your application can take control of how your assets are served. All you need to do to get this functionality of both gems is add the `rails_on_heroku` gem to your project.

## Why Didn't I need this before?

Why do you need to include this gem in Rails 4 and not Rails 3? Rails4 is getting rid of the concept of plugins. Before libraries were easily distributed as Gems and in the form of Engines, Rails had a folder `vendor/plugins`. Any code you put there would be initialized much like a Gem is today. This was a very simple and easy way to share and use libraries, but it wasn't very maintainable. You could use a library, and make a change locally and then deploy which makes your version incompatible from future versions. Even worse there was no concept of versioning aside from source control, so semantic versioning was out of the question. For these reasons and more Rails3 deprecated plugins. With Rails4 plugins have been removed completely. Why does this affect your app on Heroku?

In the past Heroku has used plugins as a safe way to configure your application where code was needed. While we advocate [separating config from code](http://12factor.net), this was the only option if we wanted your apps to work with no changes from you. With Rails3 Heroku will add the asset serving and standardout logging plugins to your app automatically. With Rails4, Heroku needs you to add these libraries to your Gemfile.

It is important to note that unlike Gems, plugins do not have a dependency resolution phase like what happens when you run `bundle install`. Heroku does not and will not add anything to your Gemfile on compilation.


## The Future

We will be working with Rails and the Rails core team to make future versions of Rails work on Heroku out of the box. Until then you'll need to add this gem to your project.


