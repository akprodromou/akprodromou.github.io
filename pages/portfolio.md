---
layout: default
title: Portfolio
permalink: /portfolio/
---


<div class="container-fluid">
  <div class="row">
    <!-- Empty left column -->
    <div class="col-1"></div>

    <!-- Main content (3 middle columns) -->
    <div class="col-10 col-md-8 mx-auto">
      <h1>Featured Projects</h1>

      <div class="row g-4">
        {% for project in site.data.projects %}
          <div class="col-md-4 col-sm-6">
            <div class="card h-100 project-card">
              <a href="{{ project.link }}" target="_blank">
                <img src="{{ project.image }}" class="card-img-top" alt="{{ project.title }}">
              </a>
              <div class="card-body">
                <a href="{{ project.link }}" target="_blank" class="text-decoration-none text-dark">
                  <h3 class="card-title h5">{{ project.title }}</h3>
                </a>
                <p class="card-text">{{ project.description }}</p>
                <div class="tags">
                  {% for tag in project.tags %}
                    <span class="badge bg-secondary me-1">{{ tag }}</span>
                  {% endfor %}
                </div>
              </div>
            </div>
          </div>
        {% endfor %}
      </div>
    </div>

    <!-- Empty right column -->
    <div class="col-1"></div>
  </div>
</div>


