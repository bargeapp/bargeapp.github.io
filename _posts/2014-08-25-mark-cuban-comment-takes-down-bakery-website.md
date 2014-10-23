---
layout:     post
title:      Comment by Mark Cuban takes down Bakery Website
categories: stories
---

Business Insider [has the story](http://www.businessinsider.com/mark-cuban-cookies-2014-8). Basically while speaking as a guest on [The Dan Patrick Show](http://www.danpatrick.com/) Mark made mention of one of his investments - [Alyssa's Bakery](http://alyssascookies.com/). Mark really likes these cookies apparently:

> ...I live off these cookies...that is all I will eat anymore

[Alyssa's Bakery](http://alyssascookies.com/) was down for hours afterwards!

Some simple investigating shows that http://alyssascookies.com/ is hosted at Rackspace and is using WooCommerce (a Wordpress ecommerce plugin). The server identifies itself as Apache 2.2 though it's hard to tell if they're using Rackspace's cloud behind a load balancer or running on bare metal though I'm going to guess it's bare metal based on some known load balancer IPs.