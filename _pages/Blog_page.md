---
title:  "Blogs"
layout: archive
permalink: /Blogs/
author_profile: true
comments: true
header:
  image: assets/images/wave.jpg
---

Most of my posts are technical notes written for my own reference. It'll be great,
if anyone would find them useful.

<script src="https://lex-web-ui-codebuilddeploy-15hteepuq-webappbucket-1qvirzqv23ygb.s3.amazonaws.com/lex-web-ui-loader.min.js"></script>
<script>
  var loaderOpts = {
    baseUrl: 'https://lex-web-ui-codebuilddeploy-15hteepuq-webappbucket-1qvirzqv23ygb.s3.amazonaws.com/'
  };
  var loader = new ChatBotUiLoader.IframeLoader(loaderOpts);
  loader.load()
    .catch(function (error) { console.error(error); });
</script>

<ul>
  {% for post in site.posts %}
    {% unless post.next %}
      <font color="#778899"><h2>{{ post.date | date: '%Y %b' }}</h2></font>
    {% else %}
      {% capture year %}{{ post.date | date: '%Y %b' }}{% endcapture %}
      {% capture nyear %}{{ post.next.date | date: '%Y %b' }}{% endcapture %}
      {% if year != nyear %}
        <font color="#778899"><h2>{{ post.date | date: '%Y %b' }}</h2></font>
      {% endif %}

    {% endunless %}
   {% include archive-single.html %}
  {% endfor %}
</ul>
