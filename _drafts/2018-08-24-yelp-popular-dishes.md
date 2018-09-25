---
title:      How Yelp Used Deep Learning to Identify Popular Dishes
date:       2018-08-24 18:00:00 -0700
categories: Tech
tags:       [tech-talk, machine-learning, deep-learning, nlp]
layout:     single
classes:    wide
header:
  teaser:   /assets/images/yelp-popular-dishes/teaser.png
---

I attended a Meetup hosted by [SF Big Analytics](https://www.meetup.com/SF-Big-Analytics/){:target="_blank"} on 08/23/2018. One of the talks, given by machine learning engineer Parthasarathy Gopavarapu at Yelp, was about how Yelp developed the feature of *Popular Dishes*, [recently launched in June](https://www.yelpblog.com/2018/06/introducing-popular-dishes-on-yelp-taking-the-guesswork-out-of-what-to-order){:target="_blank"}. *Popular Dishes* lists the top dishes that are mentioned the most in the reviews for a specific restaurant. I liked the talk a lot because Partha gave very concrete pictures of how the project evolved and how the system was built as a whole, not just how a specific, abstracted machine learning problem is formulated and solved.

<figure>
  <img src="{{site.url}}/assets/images/yelp-popular-dishes/popular_dishes.png" alt="popular_dishes.png"/>
  <figcaption>Yelp's Popular Dishes feature launched in June 2018. Source: [Yelp Blog](https://www.yelpblog.com/2018/06/introducing-popular-dishes-on-yelp-taking-the-guesswork-out-of-what-to-order){:target="_blank"}.</figcaption>
</figure>

Partha's team first tackled the problem by starting with menus provided by restaurants. By performing fuzzy text matching between the menu items and users' reviews (including review text and photo captions.)



