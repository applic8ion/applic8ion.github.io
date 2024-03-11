---
layout: post
title: "[GitHub Blog] Troubleshooting"
date: 2024-03-10 23:05 -0700
categories: [BLOG, Troubleshooting]
tags: GitHub blog troubleshooting
---
# GitHub Actions Build Erros
## xxx is not an HTTPS link
`https`가 아닌 url이 존재할 경우 발생한다. `.github/workflows/pages-deploy.yml`{: .filepath}에서 `Test site` 부분에 `--enforce-https=false`를 추가한다. 문자열이 끊어지지 않도록 마지막에 `\` 추가에 유의한다.
```yml
- name: Test site
    run: |
        bundle exec htmlproofer _site \
        \-\-disable-external=true \
        \-\-ignore-urls "/^http:\/\/127.0.0.1/,/^http:\/\/0.0.0.0/,/^http:\/\/localhost/" \
        \-\-enforce-https=false \
```

# Sitemap
## sitemap.xml
[Convert an HTML site to Jekyll][convertsitetojekyll]을 참고하여 `sitemap.xml`을 작성하고, `Google Search Console`에 등록하면 error가 발생한다.
- `site.pages` loop 부분을 삭제해야 한다. 정상 동작하는 `sitemap.xml`은 아래와 같다.
    ```xml
    ---
    layout: null
    search: exclude
    ---
    {% raw %}
    <?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
            xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd"
            xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">

        {% for post in site.posts %}
        <url>
            <loc>{{ site.url }}{{ post.url }}</loc>
            {% if post.lastmod == null %}
                <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
            {% else %}
                <lastmod>{{ post.lastmod | date_to_xmlschema }}</lastmod>
            {% endif %}
            {% if post.sitemap.changefreq == null %}
                <changefreq>daily</changefreq>
            {% else %}
                <changefreq>{{ post.sitemap.changefreq }}</changefreq>
            {% endif %}
            {% if post.sitemap.priority == null %}
                <priority>0.5</priority>
            {% else %}
                <priority>{{ post.sitemap.priority }}</priority>
            {% endif %}
        </url>
        {% endfor %}
    </urlset>
    {% endraw %}
    ```
    {: file='sitemap.xml'}



[convertsitetojekyll]: https://jekyllrb.com/tutorials/convert-site-to-jekyll/#11-add-a-sitemap