{% for post in site.posts %}	
  [{{ post.date | date_to_string }} - {{ post.title }}]({{ post.url }})
  {{ post.excerpt }}
  [Read More...]({{ post.url }})
  
  ---
{% endfor %}
