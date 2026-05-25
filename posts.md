{% for post in site.posts %} 
[{{ post.date | date_to_string }} - {{ post.title }}]({{ post.url | relative_url }})
{% endfor %}

---
[foxi-tools]({{ "/foxi-tools" | relative_url }})
