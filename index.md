---
layout: default
---

<div class="posts-list">
    {% for post in site.posts %}
    <a href="{{ site.baseurl }}{{ post.url }}" class="post">
        <div class="title">{{ post.title }}</div>

        <div class="description">
            {{ post.excerpt }}
        </div>
    </a>
    {% endfor %}
</div>
