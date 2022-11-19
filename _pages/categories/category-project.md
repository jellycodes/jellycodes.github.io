---
title: "프로젝트"
layout: archive
permalink: categories/project/
author_profile: true
sidebar_main: true
# sidebar:
#         nav: "sidebar-category"
taxonomy: 프로젝트
---


{% assign posts = site.categories.Project %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
