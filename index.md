---
layout: default
---

<div class="wrapper">
  <header>
    <div class="logo-wrapper">
      <img src="{{site.logo}}" alt="Logo" />
    </div>
    <h1 class="site-title">
      <a href="{{ site.base_url }}">{{ site.title }}</a>
    </h1>

    <p class="description">{{ site.description }}</p>
    
    <div class="header-top">
      <a href="#">RSS</a>
      <a href="#">Facebook</a>
      <a href="#">Github</a>
      <a href="#">Một số blog hay nên đọc</a>
      <a href="#">Sách mình đang đọc</a>
      <a href="#">Buy me a coffee</a>
    </div>
  </header>

  <section>
    <div class="main">
      {% for post in site.posts %}
        <div class="list-items">
          <span class="home-date-indicator">{{ post.date | date: "%d-%m-%Y" }}</span>
          {{ post.tags | join: ", " }}
          <br>
          <a href="{{ post.url }}">{{ post.title }}</a>
        </div>
      {% endfor %}
    </div>
  </section>
</div>