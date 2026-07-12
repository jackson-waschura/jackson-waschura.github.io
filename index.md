---
layout: page
title: Home
permalink: /
---

<div class="leaf">

  <div class="masthead">
    <h1 class="site-title">Jackson Waschura</h1>
    <p class="site-tagline">machine learning, mountain trails, and the books between</p>
  </div>

  <div class="prose dropcap-host">
    <p>Hi! My name is Jackson. I&rsquo;m fascinated by machine learning and have spent the last four years building, training, and deploying neural networks at <a href="https://zoox.com/">Zoox</a> to enable autonomous driving. I&rsquo;m always searching to learn more about this alchemical art and find new ways to put it to use.</p>

    <p>In my free time I like to play board games, make cocktails, <a href="/hiking/">hike trails</a>, make art, and try new food.</p>

    <p>I&rsquo;m currently looking to meet new people in the SF Bay Area passionate about machine learning and life-long learning. If you think we&rsquo;d have a stimulating conversation, or if you have recommendations for any of the hobbies I listed above, please reach out!</p>
  </div>

  <div class="fleuron" aria-hidden="true">
    <svg viewBox="0 0 100 24" xmlns="http://www.w3.org/2000/svg" fill="currentColor">
      <path d="M50,13 C42,4 24,3 9,12 C22,10.5 36,10.8 50,13 Z"/>
      <path d="M50,13 C58,4 76,3 91,12 C78,10.5 64,10.8 50,13 Z"/>
      <path d="M50,9.4 L53.2,13 L50,16.6 L46.8,13 Z"/>
    </svg>
  </div>

  <h2 class="chapter-title sc">Writing</h2>
  <p class="dek">essays, filed as they&rsquo;re finished</p>

  <ul class="post-index">
    {% for post in site.posts %}
    <li>
      <a class="post-row" href="{{ post.url | relative_url }}">
        <span class="post-title">{{ post.title | escape }}</span>
        <span class="post-date">{{ post.date | date: "%b %-d, %Y" }}</span>
      </a>
    </li>
    {% else %}
    <li class="empty-note">Nothing published yet &mdash; check back soon.</li>
    {% endfor %}
  </ul>

</div>
