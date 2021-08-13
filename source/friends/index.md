---
layout: friends # 必须
title: 我的朋友们 # 可选，这是友链页的标题
---

{% issues sites | api=https://api.github.com/repos/Fieshy/friends/issues?sort=updated&state=open&page=1&per_page=100&labels=active %}
