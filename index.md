{% for post in site.posts limit:1 %}	
  {{ post.date | date_to_string }}
  {{ post.content }}
{% endfor %}

---
[All Posts]({{ site.baseurl}}/posts)
