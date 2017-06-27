---
layout: default
title: Archive
---
<div class="posts">
  {% for post in site.posts %}
  {% if post.layout == "post" %}
    <div class="post-list-wrapper">
      <div class="post-list-date">
        {{ post.date | date_to_string }}
      </div>
      <div class="post-list-title">
        <a href="{{ post.url }}">
          {{ post.title }}
        </a>
      <div class="post-list-preview">
       {{ post.content | strip_html | truncatewords:25}}<br>
      </div>         
      </div>

    </div>
  {% endif %}
  {% endfor %}
</div>
