---
layout: default
permalink: /blog/
---

# Tech Blog


  {% for post in site.posts %}
  <article>
    <div class="blog-img-div">
        <img src="/assets/imgs/{{ post.image }}" />
    </div>
    <div class="blog-text-div">
        <a href="{{ post.url }}"><h2 class="blog-article-title">{{ post.title }}</h2></a>
        <div class="blog-article-tagline">{{ post.categories }}</div>
        
        <p>{{ post.description }}</p>
    </div>
  </article>
  <div class="clearfix"></div>

  {% endfor %}
	