---
layout: default
---
In software world we developers ofter share with others. We pack our code
into libraries and allow other coders to use them. This increases our
productivity as we can focus now on real applications instead of doing
core things again and again. This is a standard approach for 2019. It
wasn't like this in the past: there was no Internet and other means of
communication wasn't that effective.

Today, as most of retro devs are using cross development, lack of 
internet support in your 8-bit machine is no longer an issue. Processing
power is also not a problem. So why not
incorporate productivity tools such as open source libraries, automated
build, continuous integration, automated testing into development process
for 8-bit? The [c64lib] is here to help you.

## Pinned documents

<div class="tiles">
{% assign sorted_pages = site.pages | sort: "title" %}
{% for page in sorted_pages %}
  {% if page.pinned == true %}
  <div class="tile">
  <a href="{% if site.baseurl == "/" %}{{ page.url }}{% else %}{{ page.url | prepend: site.baseurl}}{% endif %}">{{ page.title }}</a>
  {{ page.description | markdownify }}
  </div>
  {% endif %}
{% endfor %}
</div>

[c64lib]: https://github.com/c64lib
