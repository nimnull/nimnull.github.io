<!DOCTYPE html>
<html>
    {% include header.md %}
    <body>
        <header>
            {% include navigation.md %}
            <div class="container">
                <h3><small>{{ page.date | date: "%d.%m.%Y" }}</small> {{ page.title }}</h3>
            </div>
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






