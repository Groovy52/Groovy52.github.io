
# Paper Reviews

Browse my research paper reviews below.

<ul>
  {% for post in site.posts %}
    <li style="margin:6px 0;">
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      <span style="opacity:.6;"> — {{ post.date | date: "%Y-%m-%d" }}</span>
    </li>
  {% endfor %}
</ul>
