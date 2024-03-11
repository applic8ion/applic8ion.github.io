---
# the default layout is 'page'
icon: fas fa-t
order: 4
---
{% for item in site.data.terms %}
**{{ item.name }}**
: {{ item.description }}
{% endfor %}