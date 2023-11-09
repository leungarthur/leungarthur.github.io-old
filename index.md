---
layout: default
---

![pic of me](/assets/portrait.jpg){: width="250" }

I'm a PhD student at the <a href='https://eeb.utoronto.ca'>Dept. of Ecology & Evolutionary Biology</a>, University of Toronto, Canada. I'm working with Prof. Rowan Sage on the evolution of C<sub>4</sub> photosynthesis, a key innovation in flowering plants that promoted diversification into warm and semi-arid habitats. I am interested in clarifying the environmental factors and physiological mechanisms that facilitated C<sub>4</sub> evolution. I received my BSc from Western University, where I worked with Prof. Norm Hüner on photosynthesis in boreal trees grown under high temperature and CO<sub>2</sub> conditions.

**Blog**
{% for post in site.posts %}
- {{ post.date | date_to_string }} [{{ post.title }}]({{ post.url | relative_url }})<br>

{% endfor %}
[See archive...](/archive)

**Funding acknowledgements**
- Queen Elizabeth II/Charles E. Eckenwalder Scholarship in Science and Technology
- NSERC Undergraduate Student Research Award
- NSERC Canadian Forest Sector supplement

**Contact**
- Email (art.leung \<at\> mail.utoronto.ca)
- Twitter ([@aleungplants](https://twitter.com/aleungplants))
- Bluesky ([@aleungplants.bsky.social](https://bsky.app/profile/aleungplants.bsky.social))
