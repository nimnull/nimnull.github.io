<!DOCTYPE html>
<html>
    {% include header.md %}
    <body>
        <header>
            <header>
                {% include navigation.md %}
                <h1><small>{{ date | date: "%d.%m.%Y" }}</small> {{ title }}</h1>
            </header>
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






