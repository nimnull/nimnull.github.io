<!DOCTYPE html>
<html>
    {% include header.md %}
    <body>
        <header></header>
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
