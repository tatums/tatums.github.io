---
layout: post
title: "Form helper from console"
date: 2013-01-14 09:00:54 -0600
comments: true
categories: Rails forms
---


From the rails console.

```ruby
helper.text_field :object, :name

"<input id=\"object_name\" name=\"object[name]\" size=\"30\" type=\"text\" />"
```
