---
layout: post
title: "rails configuration"
date: 2015-03-14 10:25:26 -0500
comments: true
categories: ["rails"]
---

Occasionally I'll see things like this in a code base.
```ruby
def api_host
  if Rails.production?
    "http://prod.fake.api.url"
  else
    "http://stag.fake.api.url"
  end
end
```

I try to avoid writting methods like this.  Rails provides a nice way to set environment specific variables.

http://guides.rubyonrails.org/configuring.html#custom-configuration



`config/environments/staging.rb`
```ruby
config.api_host = "http://stag.fake.api.url"
```

`config/environments/production.rb`
```ruby
config.api_host = "http://prod.fake.api.url"
```

So now you can refactor the method to this.

```ruby
def api_host
  Rails.configuration.api_host
end
```

