---
layout: post
title: Falsk, nginx, X-Accel-Redirect, GridFs
---

Так как наш сервис хранит данные в GridFS в качестве распределенного общего
хранилища (не лучшее решение, но пока что это единственный вариант для нас),
захотелось избавиться от лишней прослойки в виде wsgi приложения и избавить сам
сервис от извлечения данных. Собственно &mdash; родилась идея подключить модуль
nginx-gridfs и использовать X-Accel-Redirect для управления этой штукой.

Стандартный метод `flask.send_file` корретно работает только при работе с
Apache, да и то, если wsgi процесс работает на одном и том же хосте с ним. Или
в случае идентичной структуры каталогов с контентом, который разработчик захочет
отдавать таким способом.

Немного подумав и посмотрев на реализацию оригинального `flask.sendfile` родилось
небольшое решение:

<div>
{% highlight python %}
def x_accel_gridfs(file_field):
    headers = Headers()
    headers['X-Accel-Redirect'] = "/img/{}".format(file_field.grid_id)
    mimetype = file_field.contentType

    rv = current_app.response_class(None, mimetype=mimetype, headers=headers,
                                    direct_passthrough=True)
    cache_timeout = current_app.current_app.config['SEND_FILE_MAX_AGE_DEFAULT']

    rv.cache_control.max_age = cache_timeout
    rv.cache_control.public = True
    rv.expires = int(time() + cache_timeout)
    rv.last_modified = file_field.uploadDate

    rv.set_etag('flamaster-{}-{}-{}'.format(
        file_field.uploadDate,
        file_field.image.length,
        file_field.image.grid_id
    ))

    return rv
{% endhighlight %}
</div>

В данном случае контент извлекается из поля FileField модели mongoengine.

Для nginx конфигурация выглядит еще проще:

<div>
{% highlight nginx }
location /img/ {
    internal;
    gridfs fevent;
    mongo 127.0.0.1:27017;
}
{% endhighlight %}
</div>
