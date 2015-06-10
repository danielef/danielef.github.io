---
layout: post
title: Hello World
---

Hello World!

{% highlight ruby linenos %}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}


```ruby
def foo
  puts 'foo'
end
```
