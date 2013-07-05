---
layout: post
title: Отслеживание ajax запросов через google analytics.
---

Это не так чтобы сильно заметка, а скорее напоминание, для себя самого. В последнее время куча разных занятий не позволяет долго держать в голове материал, к которому не обращаешься хотя бы раз в неделю. В связи с этим приходится делать такие вот записи-склерозники.

Недавно в нашем проекте появилась задача добавить отслеживание различных user-generated событий к существующей статистике в GA, в том числе - нужно было складывать AJAX-запросы в одну стопку с обычными обращениями к страницам.
Мы используем асинхронный синтаксис GA, активно пропагандируемый в справке Google.
И не забываем облегчать себе работу с JS хотя бы с помощью jQuery.
В итоге, получилась такая штука:

<div>
{% highlight js %}
var _gaq = _gaq || [];

    _gaq.push(['_setDomainName', 'www.xxx.com']);
    _gaq.push(['_setAccount', 'UA-XXXXXXXX-X']);

    _gaq.push(['_setAllowHash', 'false']);
    _gaq.push(['_trackPageview']);
    _gaq.push(['_trackPageLoadTime']);

    $(document).ajaxSend(function(event, xhr, settings){
        _gaq.push(['_trackPageview', settings.url]);
    });
{% endhighlight %}
</div>
