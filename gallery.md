---
layout: page
title: Gallery
permalink: /gallery/

# Drone Shots
drone-images:
  - image_path: /assets/chidiya/chidiya3.jpg
    thumbnail: /assets/chidiya/thumbnails/chidiya3.jpg
    date: 29 March 2023
    place: Amsterdam, Netherlands
  - image_path: /assets/chidiya/chidiya5.JPG
    thumbnail: /assets/chidiya/thumbnails/chidiya5.jpg
    date: 09 April 2023
    place: Vianden, Luxembourg
  - image_path: /assets/chidiya/chidiya2.JPG
    thumbnail: /assets/chidiya/thumbnails/chidiya2.jpg
    date: 25 December 2022
    place: Tirol, Austria
  - image_path: /assets/chidiya/chidiya4.JPG
    thumbnail: /assets/chidiya/thumbnails/chidiya4.jpg
    date: 08 April 2023
    place: Namur, Belgium
  - image_path: /assets/chidiya/chidiya1.jpg
    thumbnail: /assets/chidiya/thumbnails/chidiya1.jpg
    date: 18 January 2023
    place: Amsterdam, Netherlands

# Digital Drawaing
art-images:
- image-path: /assets/chidiya/chidiya3.jpg
  thumbnail: /assets/chidiya/thumbnails/chidiya3.jpg
  caption: Long way back home :)
---
{% tabs log %}
<!-- Drone Shots Tab -->
{% tab log Drone Shots %}
{% include photo-gallery.html %}
{% endtab %}
<!-- Digital Art Tab -->
{% tab log Digital Art / Drawings %}
{% include art-gallery.html %}
{% endtab %}
{% endtabs %}
{% include copyright.html %}