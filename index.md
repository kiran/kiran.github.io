---
layout: default
title: kiranbot
---

<p>I am a software engineer, former electrical engineer, armchair science enthusiast, amateur tailor, and tiny amigurumi maker.</p>

<p>I work at <a href="https://stripe.com">Stripe</a>. I've previously built things for the New York Times, LinkedIn, Intersil, and the MIT HCI lab.</p>

<p>As the introduction implies, I flip between skills as I discover new things I want to work on. In the last year, I’ve started a container vegetable garden, learned how to swim and scuba dive, worked at a news organization, made a few purses, and picked up some woodworking skills. I'm on the lookout for new things to pick up -- contact me if you have something to teach!</p>

### Recent posts
{% for post in site.posts limit: 5 %}
- {{ post.date | date_to_string }} <span class="seperator">~</span> [{{ post.title }}]({{ post.url }})
{% endfor %}