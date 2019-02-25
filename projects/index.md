---
layout: page
title: Projects
permalink: /projects/
---
_Updating Soon! Till then find projects [here](https://jogendra.github.io/my-old-personal-website/projects) (NOT UPDATED)_

<div class="cards">
{% for projects in site.projects reversed %}
<div class="card">
  <img class="card-img-top" src="{{ projects.image }}" alt="{{ projects.title }}" style="width:100%;">
  <div class="card-body">
    <h3 class="card-title">{{ projects.title }}</h3>
    <p class="card-text">{{ projects.description }}</p>
    <a href="{{ projects.github }}"><i class="fa fa-github"></i>&nbsp;&nbsp;Project GitHub Link</a><br>
    <a href="{{ projects.weblink }}"><i class="fa fa-link"></i>&nbsp;&nbsp;Web Link</a>
    <p class="card-language"><i class="fa fa-code"></i>&nbsp;&nbsp;{{ projects.languages }}</p>
    </div>
              <ul class="actions">
								<li><a class="github-button" href="{{ projects.github }}/archive/master.zip" aria-label="Download on GitHub">Download</a></li>
								<li><a class="github-button" href="{{ projects.github }}" data-show-count="true" aria-label="Star on GitHub">Star</a></li>
								<li><a class="github-button" href="{{ projects.github }}/fork" data-show-count="true" aria-label="Fork on GitHub">Fork</a></li>
							</ul>
</div>
{% endfor %}
<!-- GitHub buttons -->
<script async defer src="https://buttons.github.io/buttons.js"></script>
</div>
