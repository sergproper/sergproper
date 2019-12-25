# sergproper
First
https://tutorial.djangogirls.org/ru/deploy/
описание работы с Django:
загрузка, создание локального сервера, настройка локальных переменных и т.п. 

создание виртуального окружения (оно также называется virtualenv)
mkdir Django
cd Django
Мы создадим виртуальное окружение под именем serg1
python -m venv serg1
Указанная выше команда создаст директорию serg1
Запускаем виртуальное окружение, выполнив:
...\Django> myvenv\Scripts\activate
После этого перед именами пути появляется (serg1) C:\users\...
Надо удостовериться, что установлена последняя версия pip — программы, которую мы используем для установки Django.
(myvenv) python -m pip install --upgrade pip
Для начала создаем файл requirements.txt внутри директории Django/, в файл нужно добавить такой текст:

Django~=2.2.4
Теперь выполним команду pip install -r requirements.txt, чтобы установить Django.
===================================================================================
Первый шаг — создать новый проект Django. В сущности, это значит, что мы запустим несколько стандартных 
скриптов из поставки Django, которые создадут для нас скелет проекта. 
Это просто куча каталогов и файлов, которые мы используем позже.
Не забудь: нужно запускать все команды в virtualenv. 
Если ты не видишь в командной строке префикса (serg1), то необходимо активировать virtualenv командой ...\Django> myvenv\Scripts\activate
В Windows следует запустить следующую команду (точка . в конце обязательна!!!):

(serg1) C:\Users\...\Django> django-admin.exe startproject mysite .
Точка . крайне важна, потому что говорит скрипту установить Django в текущем каталоге (который и обозначается сокращённо точкой .)
django-admin — это скрипт, который создаст необходимую структуру директорий и файлы для нас

 внесём изменения в mysite/settings.py
 TIME_ZONE = 'Europe/Moscow'
 LANGUAGE_CODE = 'ru-ru'
 в конце файла после переменной STATIC_URL добавить новую — STATIC_ROOT:
 STATIC_ROOT = os.path.join(BASE_DIR, 'static')
 изменить:
 ALLOWED_HOSTS = ['127.0.0.1', '.pythonanywhere.com']
 Чтобы создать базу данных для нашего блога, набрать в командной строке: 
 C:\Users\...\Django>python manage.py migrate
 Пришло время запустить веб-сервер и посмотреть, работает ли наш веб-сайт!
 апустим веб-сервер из командной строки: 
 C:\Users\...\Django>python manage.py runserver
 Теперь тебе нужно проверить, работает ли веб-сайт — надо открыть другой браузер (окно) и набрать следующий адрес:
http://127.0.0.1:8000/

Создание приложения
Для аккуратности мы создадим отдельное приложение в нашем проекте. 
Для создания приложения набрать следующую инструкцию в командной строке 
(serg1) C:\Users\...\Django> python manage.py startapp blog
В проекте появится новая папка blog
После того, как приложение создано, нужно сообщить Django, что теперь он должен его использовать. 
Мы сделаем это с помощью файла mysite/settings.py. 
Нужно найти переменную INSTALLED_APPS и добавить к списку 'blog', (запятая важна!) прямо перед ].

В файле blog/models.py мы определяем все Модели — модель записи для блога также пойдёт сюда.

Открыть файл blog/models.py, удалить весь текст и вставить на его место следующий код:

blog/models.py
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

Последним шагом будет добавление нашей модели в базу данных. 
Сначала мы должны дать Django знать, что сделали изменения в нашей модели (мы её только что создали!). 
Набрать (serg1) C:\Users\...\Django> python manage.py makemigrations blog

Чтобы добавлять, редактировать и удалять записи, для которых мы только что создали модель, мы используем панель управления администратора Django.

Откроем файл blog/admin.py и заменим его содержимое на:

from django.contrib import admin
from .models import Post
admin.site.register(Post)

Примечание: мы импортировали (включили) модель Post, которую определили в ранее (см. class Post). Чтобы модель стала доступна на странице администрирования, нужно зарегистрировать её при помощи admin.site.register(Post)
Теперь можно взглянуть на модель Post (не забsnm запустить веб-сервер командой python manage.py runserver). 
Перейnи в браузер и набрать адрес http://127.0.0.1:8000/admin/. Будет показана страница авторизации.

Чтобы залогиниться, сначала нужно создать суперпользователя (superuser) — пользователя, который имеет полный доступ к управлению сайтом. 
Для этого открыть еще одно окно cmd и и активировать в нём виртуальное окружение (см.выше)
Набрать команду python manage.py createsuperuser и сгенерировать имя и пароль (пароль может быть не виден)

Вернуться в браузер и войти в систему при помощи имени пользователя и пароля. Должна открыться панель управления Django. Можно добавить несколько постов.
Официальная документация об администрировании Django: https://docs.djangoproject.com/en/1.11/ref/contrib/admin/.
