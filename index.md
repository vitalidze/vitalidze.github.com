---
layout: page
title: Vitaly Litvak blog
---
{% include JB/setup %}

## About

I am a Java Developer from Russia, Moscow, Zelenograd. Graduated from Moscow State University of Electronic Technology in 2008 I am working more than six years in same software company developing ERP and accounting solution for hedge funds, fund of funds and other investment groups.

I have two beatiful beloved children - Emily (daughter) and Ilya (son), like bicycles (in summer) and snowboarding (i.e. in winter).

In this site I am going to post info, which I found noteworthy

## Blog Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


