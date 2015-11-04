---
layout: default
title: Sewing
permalink: /sewing/
---

<div class="home">

  <h1 class="page-heading">Posts about Sewing</h1>

    {% for post in site.posts %}
      {% if post.categories contains 'sewing' %}
	      <article class="post-preview">
	        {% if post.thumbnail %}
	          <span class="post-img">
	              <img class="thumbnail" src="{{ post.thumbnail }}" width="50" height="50" title="{{ post.title }}">
	          </span>
	        {% endif %}
	        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
	        <h2>
	          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
	        </h2>
	        {{ post.content | strip_html | truncatewords:20 }}
	      </article>
      {% endif %}
    {% endfor %}

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | prepend: site.baseurl }}">via RSS</a></p>

</div>