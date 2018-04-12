{% for post in site.posts %}	
    [{{ post.dat }} - {{ post.title }}]({{ post.url }})
{% endfor %}
