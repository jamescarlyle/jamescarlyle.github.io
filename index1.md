---
title : home page
layout: default
---
See my [LinkedIn](https://www.linkedin.com/in/jamescarlyle), [Twitter](https://twitter.com/jwgcarlyle) and [Facebook](https://www.facebook.com/jwgcarlyle) profiles. I also do a little night-time coding on [GitHub](https://github.com/jamescarlyle)

I'm sometimes on [Skype](skype:jamescarlyle?call)

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

James
