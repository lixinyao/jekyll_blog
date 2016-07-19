---
title: 李昕垚 | 岁寒然后知松柏之后凋也
layout: page
---

<ul class="listing">
{% for post in site.posts %}
  {% capture y %}{{post.date | date:"%Y"}}{% endcapture %}
  {% if year != y %}
    {% assign year = y %}
    <li class="listing-seperator">{{ y }}</li>
  {% endif %}
  <li class="listing-item">
    <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time>
    <a href="{{ site.url }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
  </li>
{% endfor %}
</ul>

<div id="post-pagination" class="paginator">

  {% if paginator.previous_page %}
    {% if paginator.previous_page == 1 %}
    <a href="/blog"><上一页</a>
    {% else %}
    <a href="/blog/page{{paginator.previous_page}}"><上一页</a>
    {% endif %}
  {% else %}
    <span class="previous disabled"><上一页</span>
  {% endif %}

      {% if paginator.page == 1 %}
      <span class="current-page">1</span>
      {% else %}
      <a href="/blog">1</a>
      {% endif %}

    {% for count in (2..paginator.total_pages) %}
      {% if count == paginator.page %}
      <span class="current-page">{{count}}</span>
      {% else %}
      <a href="blog/page{{count}}">{{count}}</a>
      {% endif %}
    {% endfor %}

  {% if paginator.next_page %}
    <a class="next" href="/blog/page{{paginator.next_page}}">下一页></a>
  {% else %}
    <span class="next disabled" >下一页></span>
  {% endif %}
  (共{{ paginator.total_posts }}篇)
</div>
