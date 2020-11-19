---
layout: page
title: Archive
permalink: /archive/
---

{% for tag in site.tags %}
  <h3>{{ tag[0] }}</h3>
  <ul>
    {% for post in tag[1] %}
      <li>
        <a class="link" href="{{ post.url | relative_url }}">{{ post.title }}</a> &nbsp; <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
      </li>
    {% endfor %}
  </ul>
{% endfor %}
