---
layout: page
title: Categories
permalink: /categories/
---

<script>
function toggle(divname) {
    var el = document.getElementById(divname);
    if (el.style.display == "none") {
        el.style.display = "block";
    } else {
        el.style.display = "none";
    }
}
</script>

{% for category in site.categories %}
  {% capture category_name %}{{ category | first }}{% endcapture %}
  <h1><a onclick="toggle('{{ category_name }}')">{{ category_name }}</a></h1>
  <div id="{{ category_name }}" style="display:none">
    <ul>
      {% for post in site.categories[category_name] %}
        <li><a href="{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    </ul>
  </div>
{% endfor %}
