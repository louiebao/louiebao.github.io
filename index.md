{% for post in site.posts %}	
    <h3><a href="{{ post.url }}">{{ post.title }}<small>{{ post.date | date: "%B %e, %Y" }}</small></a></h3>
{% endfor %}
