<!DOCTYPE html>
<html>
    {% include header.md %}
    <body>
        <header>
            {% include navigation.md %}
            <h1><small>{{ page.date | date: "%d.%m.%Y" }}</small> {{ page.title }}</h1>
        </header>
        <section>
            <div class="container">
                <div class="row">
                    <div class="span12">
                        {{ content }}
                    </div>
                </div>
            </div>
        </section>
        {% include footer.md %}
    </body>
</html>






