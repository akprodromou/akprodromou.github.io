---
layout: default
title: Portfolio
permalink: /portfolio/
---


<h1>Featured Projects</h1>
<div class="projects-grid">
  {% for project in site.data.projects %}
    <div class="project-card">
      <a href="{{ project.link }}" target="_blank">
        <img src="{{ project.image }}" alt="{{ project.title }}">
      </a>
      <div class="project-card-content">
        <a href="{{ project.link }}" target="_blank">
          <h3>{{ project.title }}</h3>
        </a>
        <p>{{ project.description }}</p>
        <div class="tags">
          {% for tag in project.tags %}
            <span class="tag">{{ tag }}</span>
          {% endfor %}
        </div>
      </div>
    </div>
  {% endfor %}
</div>
