{% for post in site.posts limit:3 %}	
  {{ post.date | date_to_string }}
  {{ post.excerpt }}
  [Read More...]({{ post.url }})
  ---
{% endfor %}
