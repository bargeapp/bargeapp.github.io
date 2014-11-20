---
layout: post
title:  "New Feature: Sign up via Email (aka Manual Setup)"
date:   2014-11-20
tags: features
summary: Starting today you can sign up for Barge with your email address
author: noah
---

When we launched last week we required users to login with GitHub. Logging in with GitHub makes it super simple to get started using Barge because we can automatically configure your repositories. But GitHub permissions aren't very fine grained and so we end up having to ask for far more permissions than we actually need (see [why do you need full read/write access to all my repositories](https://www.bargeapp.com/help#security) in the Barge help page). Understandably some people didn't feel comfortable giving over the keys to the castle.

So starting today you can sign up for Barge using your email address and configure repositories manually.

In order to support these features we’ve changed the way we search your code for Barge-enabled scripts; instead of using the GitHub search API we use a deploy key to clone your repository and search through it. This update paves the way for some other really exciting features, so stay tuned.

Going forward, if you've signed up with GitHub, enabling a repository in Barge will automatically add a deploy key to that repository in GitHub. If you've signed up via email then after adding a repository you'll see a page with instructions on adding the deploy key. Note that we generate different deploy keys for each repository.

We take security very seriously. To that end:

* We generate a unique deploy key for every repository
* The private key is encrypted at rest and only decrypted immediately before cloning
* The only thing we do with the cloned repository is search for and import Barge-enabled scripts
* The files and keys are securely wiped from our virtual machine once the processes completes