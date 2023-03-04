---
layout: default
---

I'm an ecophysiologist at the <a href='https://eeb.utoronto.ca'>Dept. of Ecology & Evolutionary Biology</a>, University of Toronto, Canada.

The goal of my PhD research is to gain mechanistic understanding into the evolution of C<sub>4</sub> photosynthesis, a key innovation in flowering plants that enabled diversification into warm and semi-arid habitats. C<sub>4</sub> photosynthesis involves modifications to primary metabolic pathways which are typically under purifying or neutral selection, yet it has has evolved over 60 times independently. Thus, plant lineages containing C3, C3-C4 intermediate, and C4 species are unique study systems that allow the integration of physiology, ecology, and evolution to study how the ecosystems were assembled in the last ~30 million years.

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
- Email (art.leung <at> mail.utoronto.ca)
- Twitter ([@aleungplants](https://twitter.com/aleungplants))
