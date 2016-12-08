---
layout: page
title: Welcome to My Workshop!
tagline: Computer Programming and Music
---
{% include JB/setup %}

* For those who want to learn more about C++, refer to [C++ Study Resources](/pages/cpp-study-resource).
* For those who wanted to see Cosmos Document English Vocabulary, visit <http://ghjang.github.io/cosmos/>.

---
Here's a "posts list":

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
