---
layout: post
title: "ActiveModel::Model"
date: 2015-08-13 19:37:30 -0500
comments: true
categories: rails
---

Rails 4 brought us `ActiveModel::Model`. It provides a light weight interface that's similar to an `ActiveRecord::Base` model.

for example, I can create a Person class like so.

```ruby
class Person
  include ActiveModel::Model
  attr_accessor :name, :age

  validates :name, true

  def save
    ## Do cool stuff here...
  end
end
```


```ruby
Loading development environment (Rails 4.2.1)
irb(main):001:0> p = Person.new age: 21
=> #<Person:0x007fc6367193b0 @age=21>
irb(main):002:0> p.valid?
=> false
irb(main):003:0> p.errors
=> #<ActiveModel::Errors:0x007fc638000a68 @base=#<Person:0x007fc6367193b0 @age=21, @validation_context=nil, @errors=#<ActiveModel::Errors:0x007fc638000a68 ...>>, @messages={:name=>["can't be blank"]}>
irb(main):004:0>
```

<hr> </hr>

This is great for instances where you don't need a full database backed Active Record model. I've used them for form objects and in controllers where I have complex logic.

You can think of these as higher level abstractions above your ActiveRecord classes. Also, be conscious of the dependancy direction. An ActiveModel model can depend on an ActiveRecord model but your ActiveRecord models shouldn't depend on an ActiveModel model.


<br />
Here's a more involved example.
Lets say I have 2 ActiveRecord classes `Org` and `User`

```ruby
class Org < ActiveRecord::Base
  validates :name, presence: true
end
```

```ruby
class User < ActiveRecord::Base
  validates :first, :last, presence: true
end
```


Now I'll create an ActiveModel model (non database)

Notice the validates_each method... Its going to check each of the ActiveRecord objects and let them raise up any errors to the Signup class.

```ruby
class Signup
  include ActiveModel::Model
  attr_accessor :first, :last, :name

  validates_each :user, :org do |record, attr, value|
    unless value.valid?
      value.errors.each { |k,v| record.errors.add(k.to_sym, v) }
    end
  end

  ## must return boolean
  def save
    if valid?
      org.save && user.save
    else
      false
    end
  end

  private

    def org
      @org ||= Org.new(name: name)
    end

    def user
      @user ||= User.new(first: first, last: last)
    end
end
```
Awesome right!!

So why do all this?  Well, the single responsibilty states that every class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class.  ActiveRecord is responsible for persistence to the database. This will keep our classes with a narrow focus and allow us to refactor and create more use cases in the future. I think it's a win.  I find this strategy is generally good for one directional workflows such as signup or in a shopping app cart checkout.
