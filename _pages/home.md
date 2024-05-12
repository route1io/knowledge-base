---
layout: splash
permalink: /
title: Knowledge Base
header:
  overlay_image: /images/ZoomBG-V1-R1.jpg

feature_row:
  - image_path: images/red.PNG
    image_size: 100px
    alt: ""
    title: "Expressive and consistent syntax"
    excerpt: ""
    url: ""
    btn_label: "Learn More"
  - image_path: images/green.PNG
    alt: ""
    title: "Clear visualizations and animations"
    excerpt: ""
    url: ""
    btn_label: "Learn More"
  - image_path: images/blue.PNG
    alt: "100% free"
    title: "Completely free and open source"
    excerpt: ""
    url: "/license/"
    btn_label: "Learn More"
---

{% include feature_row %}

<h2> Recent articles: </h2>

{% for post in site.posts limit:3 %}
  {% include archive-single.html %}
{% endfor %}

[See all blog posts...]({{site.url}}{{site.baseurl}}/blog/){: .btn .btn--info}