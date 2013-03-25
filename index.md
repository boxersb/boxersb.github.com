---
layout: default
title: boxersb
tagline: Supporting tagline
---
{% include JB/setup %}

{% assign post = site.posts.first %}

<div class="entry-content front-page">
	<header>
		<div class="unit-head">
			<div class="unit-inner unit-head-inner">
				<h2 class="h2 entry-title"><a href="{{ post.url }}">{{ post.title }}</a></h2>
			</div><!-- unit-inner -->
		</div><!-- unit-head -->
	</header>

	<div class="meta">
		<p class="date-publish">
			Published: 
			<span class="month"><abbr>{{ post.date | date: '%B' }}</abbr></span>
			<span class="day">{{ post.date | date: '%d' }}</span>
			<span class="year">{{ post.date | date: '%Y' }}</span>
		</p>
		<ul class="list-category list-linear">
			<li class="list-head">category: </li>
			{% assign categories_list = post.categories %}
			{% include JB/categories_list %}
		</ul>
		<ul class="list-tag list-linear">
			<li class="list-head">tags: </li>
			{% assign tags_list = post.tags %}
			{% include JB/tags_list %}
		</ul>
	</div>
	<div class="entry-content">
		{{ post.content }}
	</div>
	<div class="misc-content">
		<div class="comment">
		{% include JB/comments %}
		</div>
	</div>
</div>