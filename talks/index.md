---
layout: page
title: Talks
permalink: /talks/
---
<br>
<div align="center"> A list of talks I have presented at conferences/summit and meetups/workshop. </div>

<div>
{% for talks in site.talks reversed %}
  <br>
  {% if talks.date > site.time %}
    {% if forloop.first %}
      <h2 class="talks-section" id="upcoming">Upcoming</h2>
      <hr class="talks-hr">
    {% endif %}
  {% else %}
    {% assign currentyear = talks.date | date: "%Y" %}
    {% if currentyear != previousyear %}
      <h2 class="talks-section" id="y{{ talks.date | date: "%Y"}}">{{ currentyear }}</h2>
      <hr class="talks-hr">
      {% assign previousyear = currentyear %}
    {% endif %}
  {% endif %}


    <div class="talks">
      <span class="post-title"><strong><big> {{ talks.title }} </big></strong></span>
      {% if talks.subtitle %}
      <span class="post-date talks-subtitle"> - {{ talks.subtitle }} </span>
      {% endif %}
      <br>

      <i class="fa fa-comments-o" aria-hidden="true"></i>&nbsp;&nbsp;
      {% if talks.event-url %}
      <a href="{{ talks.event-url }}"
       title="{% if talks.event-fulltitle %}{{ talks.event-fulltitle }}{% else %}{{ talks.event }}{% endif %}
{{ talks.event-url }}">
      {% endif %}

      {{ talks.event }}{% if talks.event-fulltitle %}: {{ talks.event-fulltitle }}{% endif %}
      {% if talks.event-url %}</a>{% endif %}
      <br>

      <span class="location">
        <i class="fa fa-map-marker" aria-hidden="true"></i>&nbsp;&nbsp;&nbsp;
        {{ talks.location }}
      </span>
      <br>

      <i class="fa fa-calendar" aria-hidden="true"></i>&nbsp;&nbsp; {{ talks.date | date: "%B %-d, %Y" }}
      <br>

      {% if talks.slides %}
        <span class="talks-resource">
          <i class="fa fa-file-pdf-o" aria-hidden="true"></i>&nbsp;&nbsp;
          {% if talks.slides contains ".pdf" %}
            <a href="{{ site.pdfs }}/{{ talks.slides }}">
          {% else %}
            <a href="{{ talks.slides }}">
          {% endif %}
          Slides</a>
        </span>
        &nbsp; &nbsp;
      {% endif %}

      {% if talks.video %}
        <span class="talks-resource"><i class="fa fa-file-video-o" aria-hidden="true"></i>&nbsp;&nbsp; <a href="{{ talks.video }}">Video</a></span>
      {% endif %}

      {% if talks.post %}
        &nbsp; &nbsp;
        <span class="talks-resource">
          <i class="fa fa-file-text-o" aria-hidden="true"></i>&nbsp;&nbsp;
          <a href="{{ site.url }}{{ talks.post }}">Post</a>
        </span>
      {% endif %}

      {% if talks.featured %}
        &nbsp; &nbsp;
        <span class="talks-resource"><i class="fa fa-file-text-o" aria-hidden="true"></i>&nbsp;&nbsp; <a href="{{ talks.featured }}">Featured</a></span>
      {% endif %}
    </div>
{% endfor %}
</div>
