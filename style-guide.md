---
layout: style-guide
title: Style Guide
---

{% assign entries = site.colours %}
{% assign componentsByType = site.components | group_by:"type" %}

<div markdown="0">
<select name="newurl" id="component-select" onChange="window.location.replace(this.options[this.selectedIndex].value)" aria-label="Select a Component" markdown="0">
  <option selected markdown="0">Select a Component</option>
  <option value="#guide-color-palettes" markdown="0">Colors</option>
  {% for type in componentsByType %}
  <option value="#guide-{{ type.name }}" markdown="0">{{ type.name | capitalize }}</option>
  {% for entry in type.items %}
  <option value="#guide-{{ entry.title | slugify }}" markdown="0">&nbsp;&nbsp;&nbsp;{{ entry.title }}</option>
  {% endfor %}
  {% endfor %}
</select>
</div>

<h2 id="guide-color-palettes" class="cf">Colors</h2>
{% for entry in entries %}
  {% include style-guide/component-color.html %}
{% endfor %}
{% for type in componentsByType %}
<h2 id="guide-{{ type.name }}" class="cf">{{ type.name | capitalize }}</h2>
{% for entry in type.items %}
{% include style-guide/component.html %}
{% endfor %}
{% endfor %}
