---
layout: post
title: "Migrate a Single Database Migration"
date: 2013-01-21 08:43:00 -0600
comments: true
categories: Rails migration
---
```ruby
rails console
require "db/migrate/20121130134444_add_column_backorder_to_orders.rb"
AddColumnBackorderToOrders.up
```
