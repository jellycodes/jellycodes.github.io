---
title: "Work"
layout: archive
permalink: categories/Work/
author_profile: true
sidebar_main: true
# sidebar:
#         nav: "sidebar-category"
taxonomy: Work
---


{% assign posts = site.categories.Work %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
