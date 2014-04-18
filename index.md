---
layout: index
title: WeblogNG
tagline: News
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
    <div class="well">
    {{ post.content }}
    </div>
  {% endfor %}
</ul>



