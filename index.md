---
layout: default
---

<div class="posts-list">
    {% for post in site.posts %}
    <div class="post">
        <div class="title">
            <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
        </div>

        <div class="description">
            {{ post.description }}
        </div>
    </div>
    {% endfor %}
</div>
