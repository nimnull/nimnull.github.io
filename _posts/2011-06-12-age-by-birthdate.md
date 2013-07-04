---
layout: post
title: Получение возраста по дате рождения
---

> В рамках переноса самых замечательных постов

{% highlight py %}
@property
def age(self):
   today = date.today()
   years = today.year - self.birth_date.year

   if all((x >= y) for x, y in zip(today.timetuple(), self.birth_date.timetuple())):
       age = years

   else:
       age = years - 1

   return age
{% endhighlight %}

Еще вариант:

{% highlight py %}
today = date.today()
years = today.year – self.birth_date.year
age = years – int(today.timetuple()[1:] < self.birth_date.timetuple()[1:])
{% endhighlight %}

И самый удачный:

{% highlight py %}
birthday = date(1990, 01,23)
age = datetime.date.fromordinal(datetime.date.today().toordinal() – birthday.toordinal())
{% endhighlight %}
