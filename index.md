---
layout: default
title: boxersb
tagline: Supporting tagline
---
{% include JB/setup %}

{% assign recent_post = site.posts.first %}

<script>
	location.replace("{{ recent_post.url }}");
</script>