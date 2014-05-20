---
layout: post
title:  "RVM to install Ruby 2.0.0"
date:   2014-04-02 22:07:58
categories: preparing
tags: [ruby, rvm]
comments: true
---

Ruby 2.0.0 is here for quite a while. If you're using RVM to manage your installed Ruby versions all you have to do is:

```
rvm install 2.0.0
```

If you haven't updated RVM for a while, you'll get the following error message:

```
Version '2.0.0' is to confusing to select ruby interpreter.
Could not detect ruby version/name for installation, please be more specific.
```

To overcome this, just update your RVM with:

```
rvm get stable
```

And then you may:

```
rvm install 2.0.0
```

Welcome to Ruby 2! :)