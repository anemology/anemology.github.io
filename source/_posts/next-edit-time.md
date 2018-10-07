---
title: Next主題修改更新時間的顯示規則
date: 2017-11-25 18:19:01
categories:
- Hexo
tags:
- hexo
- next
updated: 1990-01-01
---

只有在更新時間 > 發表時間的時候才顯示

修改 themes\next\layout\\_macro\<span style="color:red">post.swig</span>
兩個if判斷子句中加入 `and post.updated > post.date`

```html
<div class="post-meta">
  <span class="post-time">
	{% if theme.post_meta.created_at and theme.post_meta.updated_at and post.updated > post.date %}
	  <span class="post-meta-divider">|</span>
	{% endif %}

	{% if theme.post_meta.updated_at and post.updated > post.date %}
	  <span class="post-meta-item-icon">
		<i class="fa fa-calendar-check-o"></i>
	  </span>
	  {% if theme.post_meta.item_text %}
		<span class="post-meta-item-text">{{ __('post.modified') }}&#58;</span>
	  {% endif %}
	  <time title="{{ __('post.modified') }}" itemprop="dateModified" datetime="{{ moment(post.updated).format() }}">
		{{ date(post.updated, config.date_format) }}
	  </time>
	{% endif %}
  </span>
```

