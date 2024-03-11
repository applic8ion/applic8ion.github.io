---
# the default layout is 'page'
icon: fas fa-asterisk
order: 5
---
{% for item in site.data.references %}
- [{{ item.name }}]({{ item.link }})
{% endfor %}
