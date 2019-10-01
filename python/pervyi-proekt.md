# 2 Первый сайт

### 1 Создаем первый сайт

```text
django-admin.exe startproject mysite
```

###  2 Настройки 

Давай внесём изменения в `mysite/settings.py`, необходимо добавить в настройки информацию о расположении статических файлов \(мы познакомимся со статическими файлами и CSS в следующих главах\). Спустись в _конец_ файла и после переменной `STATIC_URL` добавь новую — `STATIC_ROOT`

```text
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

### 3 Создаем БД

```text
python manage.py migrate
```

### 4 Запуск веб-сервера

```text
python manage.py runserver
```

### 5 Создание приложения

```text
python manage.py startapp blog
```

После того, как приложение создано, нам нужно сообщить Django, что теперь он должен его использовать. Мы сделаем это с помощью файла `mysite/settings.py`. Нам нужно найти `INSTALLED_APPS` и добавить к списку `'blog',` прямо перед `]`

```text
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
]
```

### 6 Создание модели записи в блоге

Открой файл `blog/models.py`, удали весь текст и вставь на его место следующий код:

blog/models.py

```text
from django.conf import settings
from django.db import models
from django.utils import timezone


class Post(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=200)
    text = models.TextField()
    created_date = models.DateTimeField(default=timezone.now)
    published_date = models.DateTimeField(blank=True, null=True)

    def publish(self):
        self.published_date = timezone.now()
        self.save()

    def __str__(self):
        return self.title
```

> Убедись, что использовала два символа нижнего подчёркивания \(`_`\) с обеих сторон от метода `str`. Это соглашение часто используется при программировании на Python, и иногда его называют "dunder" \(сокращение от англ. "double-underscore"\).

Строки, начинающиеся с `from` или `import`, открывают доступ к коду из других файлов. Так что вместо того, чтобы копировать и вставлять один и тот же код во все файлы, ты можешь сослаться на него при помощи `from ... import ...`.

`class Post(models.Model):` — эта строка определяет нашу модель \(`объект`\).

* `class` — это специальное ключевое слово для определения объектов.
* `Post` — это имя нашей модели, мы можем поменять его при желании \(специальные знаки и пробелы использовать нельзя\). Всегда начинай имена классов с прописной буквы.
* `models.Model` означает, что объект Post является моделью Django, так Django поймет, что он должен сохранить его в базу данных.

Дальше мы задаем свойства, о которых уже говорили: `title`, `text`, `created_date`, `published_date` и `author`. Чтобы это сделать, нам нужно определиться с типом полей \(это текст? число? дата? ссылка на другой объект? например, на пользователя?\).

* `models.CharField` — так мы определяем текстовое поле с ограничением на количество символов.
* `models.TextField` — так определяется поле для неограниченно длинного текста. Выглядит подходящим для содержимого поста, верно?
* `models.DateTimeField` — дата и время.
* `models.ForeignKey` — ссылка на другую модель.

Мы не будем объяснять каждую запятую, поскольку на это уйдет слишком много времени. Ознакомься с официальной документаций Django: если хочешь узнать больше о полях моделей и о том, как определять разные объекты, то эта ссылка может помочь: \([https://docs.djangoproject.com/en/1.11/ref/models/fields/\#field-types](https://docs.djangoproject.com/en/1.11/ref/models/fields/#field-types)\).

### 7 Методы

Что насчёт `def publish(self):`? Это как раз метод `публикации` для записи, о котором мы говорили. `def` означает, что создаётся функция/метод, а `publish` — это название этого метода. Можно изменить имя метода, если хочешь. Существует правило для имён функций: нужно использовать строчные буквы, а пробелы заменять на подчёркивания. Например, метод, вычисляющий среднюю цену, может называться `calculate_average_price`.

Методы часто `возвращают` что-то. Например, метод `__str__`. В наше случае после вызова метода `__str__()` мы получим текст \(**строку**\) с заголовком записи.

Также обрати внимание, что оба метода `def publish(self):` и `def __str__(self):` внутри класса имеют дополнительный отступ. Поскольку в Python важны отступы, нам необходимо использовать их для методов внутри класса — иначе методы не будут принадлежать к классу, и при запуске программы может получиться что-то неожиданное.

Если тема моделей тебе до сих пор непонятна — не стесняйся обратиться к тренеру! Мы знаем, что она действительно сложна, особенно когда ты изучаешь параллельно объекты и функции. Но мы надеемся, что всё это больше не кажется тебе магией!

### 8 Создаем таблицы моделей в базе данных

Последним шагом будет добавление нашей модели в базу данных. Сначала мы должны дать Django знать, что сделали изменения в нашей модели \(мы её только что создали!\). Набери `python manage.py makemigrations blog`. Должно получиться примерно так:

command-line

```text
(myvenv) ~/djangogirls$ python manage.py makemigrations blog
Migrations for 'blog':
  blog/migrations/0001_initial.py:
  - Create model Post
```

**Примечание:** не забудь сохранить отредактированные файлы. Иначе твой компьютер выполнит команду с их предыдущей версией, и могут появиться неожиданные ошибки.

Django создал для нас файл с миграцией для базы данных. Набери `python manage.py migrate blog`, результат должен быть следующим:

command-line

```text
(myvenv) ~/djangogirls$ python manage.py migrate blog
Operations to perform:
  Apply all migrations: blog
Running migrations:
  Rendering model states... DONE
  Applying blog.0001_initial... OK
```

Ура! Модель записи для блога теперь в базе данных, и было бы неплохо посмотреть на неё, верно? Тогда переходи к следующей главе!

