---
layout: post
title:  "Redirect a doi to off-campus access URL with a browser extension"
---

When I'm off-campus, my browser never remembers that it was logged into my university account, so I have to go to the library website and log in there.

I have been using Redirector ([github](https://github.com/einaregilsson/Redirector)), a browser extension that allows redirecting of URLs that match a specific pattern to another URL of your choice.

![include pattern is *doi.org/*, and redirects to OFFCAMPUSURLhttps://doi.org/$2](/assets/2023-05-09-redirector.png)

So whenever I can't get a paper because I'm off campus, I just click the doi link and the browser extension will redirect me to a URL with the off-campus access URL appended to the front, which will either let me log in or take me straight to the paper. Nice!

This also works with Sci-Hub (for educational purposes), but there haven't been new uploads to Sci-Hub since 2021.
