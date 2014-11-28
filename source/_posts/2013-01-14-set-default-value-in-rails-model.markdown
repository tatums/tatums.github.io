---
layout: post
title: "Set default value in rails model"
date: 2013-01-14 08:00:37 -0600
comments: true
categories:
---


I like to set default values in my models. This is the method I like to use.

```ruby
def after_initialize
  if new_record?
    self.upper_quantity ||= 0.0
  end
end
```
