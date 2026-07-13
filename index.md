---
layout: page
title: Home
permalink: /
---

<div class="leaf">

  <div class="masthead">
    <h1 class="site-title">Jackson Waschura</h1>
    <p class="site-tagline">neural networks, forest trails, and lifelong learning</p>
  </div>

  <div class="prose dropcap-host">
    <p>Hi! My name is Jackson. I&rsquo;m fascinated by machine learning and currently working at <a href="https://www.mindrobotics.com/">Mind Robotics</a> training robots to automate dextrous manufacturing tasks. Previously I spent four years building, training, and deploying neural networks at <a href="https://zoox.com/">Zoox</a> to enable autonomous driving. I&rsquo;m always searching to learn more about this alchemical art and find new ways to put it to use.</p>

    <p>In my free time I like to play board games, make cocktails, <a href="/hike/">hike trails</a>, create art, build video games, and try new food.</p>

    <p>I&rsquo;m always looking to meet new people in the SF Bay Area passionate about machine learning and life-long learning. If you think we&rsquo;d have a stimulating conversation, or if you have recommendations for any of the hobbies I listed above, please reach out!</p>
  </div>

  {%- include fleuron.html -%}

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
