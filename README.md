# Проект CI/CD

## Настройка удаленного сервера

1. Организация удаленного сервера;
2. Подключение к удаленному серверу через SSH командой: 
```
ssh -i SSH_KEY SSH_username@IP_remote_server
```
3. Клонирование репозитория с проектом на сервер 
```
git clone https://github.com/username/project.git
```
4. Установка необходимых зависимостей (Python, виртуальное окружение, Postgres, Redis, Celery и т.д.)
```
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```
5. Настройка .env файла:
   - Скопировать шаблон и отредактировать
   ```
   cp .env.example .env
   nano .env
   ```
   - В .env прописать параметры для продакшн окружения (база данных, секреты и т.п.)

6. Миграции базы данных
```
python manage.py migrate
```
7. Создание суперпользователя
```
python manage.py createsuperuser
```

8. Сборка статических файлов
```
python manage.py collectstatic --noinput
```

9. Запуск Gunicorn и настройка systemd
```
gunicorn --bind unix:/path/to/project.sock config.wsgi:application
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
```

10. Настройка Nginx (уже расписаны конфиги, перезагрузка)
```
sudo systemctl reload nginx
```

11. Проверка состояния приложений и логов
```
sudo systemctl status gunicorn
tail -f /var/log/nginx/access.log
```

##  Настройка workflow и CI/CD :
1. Запуск workflow в системе CI/CD GitHub Actions происходит автоматически при пуше.
2. Файл ci.yml создан в каталоге .github/workflows/ci.yml
3. Файл состоит из двух частей test и deploy
   - Ошибки тестов останавливают выполнение следующих шагов workflow
   - Проект автоматически деплоится на удаленный сервер, который запускается только после успешного завершения тестов
4. Все чувствительные данные вынесены в переменные окружения и подключены к workflow через Secrets GitHub
5. Secrets корректно подключены к шагам workflow для доступа к серверу и другим данным

## Работоспособность приложения можно проверить командами:
Вызов админ-панели http://remote_server_IP/admin/

Вызов документации по API http://remote_server_IP/swagger/
Конкретные эндпоинты можно проверить с помощю программы ```Postman```

