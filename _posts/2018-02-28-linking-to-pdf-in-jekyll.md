---
layout: single
title:  "Linking to a Pdf in Jekyll"
excerpt: "Linking to a Pdf in Jekyll: downloadable and embedded."
date:   2018-02-27 15:42:32 +0300
tags:
  - website
  - Jekyll
categories: website update
toc: TRUE
toc_label: "On this page"
toc_icon: "cog"
header:
  image: /assets/images/banner.jpg
  teaser: /assets/images/jekyll-logo.png
---
{% include toc icon="cog" title="On this page" %}

## Downloadable files
To make a pdf downloadable directly, place your pdf where you want it to be,
say assets folder. Insert the code below anywhere on your page. Voilà,
{% highlight markdown %}
you can [download pdf]({{ site.url }}/assets/mydoc.pdf) here.
{% endhighlight %}

 [Get the pdf]({{ site.url }}/assets/download/sample.pdf)

## Embedded files
An alternative is to have your pdf displayed on a Github webpage along with
other content (check my CV page).

* Make the static website/repository in Github: username.github.io
* If you already have a website, create a new static page
* Place your pdf where you want it to be (also root would do)
* Embed your pdf file:
{% highlight markdown %}
<embed src="https://username.github.io/mydoc.pdf" type="application/pdf"/>
{% endhighlight %}
