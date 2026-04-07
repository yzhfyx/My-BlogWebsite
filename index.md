---
layout: home
title: 欢迎来到我的博客
---
# 欢迎！

这是我的个人博客，我会在这里分享我的学习和思考。

## 最近文章

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) - {{ post.date | date: "%Y-%m-%d" }}
{% endfor %}

## 关于我

我是 yzh，正在学习技术博客的搭建。
