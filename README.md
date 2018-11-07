# Guia de Deploy Django => Heroku

### 1. Certifique-se que o projeto está rodando normalmente no localhost
### 2. Rode os seguintes comandos:
  - `$ pip install python-decouple`
  - `$ pip install dj-database-url`
  - `$ pip install dj-static`
### 3. Crie um arquivo no root com nome `.env`
### 4. Copie a `SECRET_KEY` do `settings.py` e coloque no `.env` assim:
  - `SECRET_KEY=COLOQUE_AQUI_A_SECRET_KEY_DO_SEU_PROJETO`
### 5. Em `settings.py` adicione isso no começo do arquivo:
  ```
  from decouple import config
  from dj_database_url import parse as dburl
  ```
### 6. Em `settings.py` altere as seguintes variáveis:
  - `SECRET_KEY = config('SECRET_KEY')`
  - `DEBUG = config('DEBUG', default=False, cast=bool)`
  - `ALLOWED_HOSTS = ['*']`
### 7. Substitua:
  ```
  DATABASES = {
    'default': {
      'ENGINE': 'django.db.backends.sqlite3',
      'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
  }
  ```
  por:
  ```
  default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')
  DATABASES = { 'default': config('DATABASE_URL', default=default_dburl, cast=dburl), }
  ```
### 8. Adicione no final do arquivo (depois de `STATIC_URL`):
  ```
  STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
  STATICFILES_DIRS = (
      os.path.join(BASE_DIR, 'static'),
  )
  ```
### 9. Em `wsgi.py`, substitua `application = get_wsgi_application()` por:
  ```
  from dj_static import Cling
  application = Cling(get_wsgi_application())
  ```
### 10. Rode o comando:
  `$ pip freeze > requirements.txt`
### 11. Adicione em requirements.txt (caso não exista):
  ```
    psycopg2
    gunicorn
  ```
### 12. Crie um arquivo no root de nome `Procfile` com:
  ```
  release: python manage.py migrate
  web: gunicorn mysite.wsgi --log-file -
  ```
### 13. Crie um app no heroku
### 14. No dashboard do heroku, vá na aba `settings`, depois em `Reveal config vars` e coloque as variáveis que estão no `.env`.
### 15. No terminal digite:
  `$ heroku login`
### 16. Depois de logado digite:
  ```
  $ heroku git:remote -a NOME_DO_SEU_APP
  $ heroku buildpacks:set heroku/python (caso não consiga, adicione o buildpack pelo dashboard do heroku)
  $ heroku config:set DISABLE_COLLECTSTATIC=1
  $ git add .
  $ git commit -m "Preparing for Deploy"
  $ git push heroku master
  ```
### 17. Pronto! Acesse seu site `https://nome_do_app.herokuapp.com/`

### 18. Para interagir com o projeto django no heroku, rode os comandos com `heroku run` na frente. ex:
  ```
  $ heroku run python manage.py createsuperuser
  $ heroku run python manage.py makemigrations
  $ heroku run python manage.py migrate
  ```
