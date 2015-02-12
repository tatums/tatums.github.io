---
layout: post
title: "Standard rails create action"
date: 2015-02-11 15:43:24 -0600
comments: true
categories: rails
---

Rails has taught me some really good best practices. Let's look at a default rails create action

```ruby
def create
  @post = Post.new(post_params)
  respond_to do |format|
    if @post.save
      format.html { redirect_to @post, notice: 'Post was successfully created.' }
    else
      format.html { render :new }
    end
  end
end
```
I try not to deviate too much from this and nor should you. If `@post.save` returns true. return a 201 status code.  If `@post.save` returns false re-render the :new action.  So you can let the user fix the form.

Often I will see things like this.

```ruby
def create
  if params[:something]
    @post = Post.create(post_params)
  else
    @post = Post.create(other_params)
  end
  redirect_to posts_path
end
```

At first glance this looks clean. But if you look closer. There is a subtle problem here. If the post object fails validation and fails to create, The action redirects to the index action where all new instaces are creating.  The @post object has lost any knowlege of the params and it will not get to oportunity to display the errors from the object.  You'll have to resort to things like `flash[:notice] = "@post.errors.full_messages.join()"`... ugh.


Active record gives us a nice interface for displaying errors on an object.




```ruby
class Post < ActiveRecord::Base
  validates :title, presence: true
end
```

```ruby
irb(main):001:0> p = Post.new
=> #<Post id: nil, title: nil, body: nil, created_at: nil, updated_at: nil>
irb(main):002:0> p.valid?
=> false
irb(main):003:0> p.errors
=> #<ActiveModel::Errors:0x007fe0ca9e4b68 @base=#<Post id: nil, title: nil, body: nil, created_at: nil, updated_at: nil>,
@messages={:title=>["can't be blank"]}>
irb(main):004:0>
```
When you call `@post.save` OR `@post.valid?` errors are added to the @post object so they can be displayed within the form.


Digging even deeper... Check out [ActiveModel::Model](http://api.rubyonrails.org/classes/ActiveModel/Model.html). It will give you this same object interface without needing database backed model.  Powerful stuff.





