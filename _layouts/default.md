<html>
    <head>
        <title>{{ page.title }} &mdash; {{ site.blog_title }}</title>
        {% include scripts.md %}
        {% include styles.md %}
    </head>
    <body>
        <header></header>
        <section>
            {{ content }}
        </section>
        <footer>{% include footer.md %}</footer>
    </body>
</html>
