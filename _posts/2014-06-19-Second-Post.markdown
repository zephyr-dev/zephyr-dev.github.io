---
layout: post
title:  "Codez"
author: "Matt Ross"
date:   2014-06-18 09:46:39
categories: jekyll update
---

<h3>Algorithm is a cool word isnt it </h3>

<p>I am not going to write you a love song </p>
<!--break-->

{% highlight ruby %}
def find_top_users
  top ={}
  User.all.each do |user|
    top[user.name] = 0
    user.snacks.each do |snack|
      if snack.purchased_status
        top[user.name] += 1
      end
    end
  end

return top
end 
{% endhighlight %}

