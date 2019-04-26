# dockerstart
Временный контейнер

docker run --rm -v $pwd:/app php:7.3.3-apache ls /app


Постоянный контейнер

пример без --rm (контейнер не пропадет теперь), -d (detached) и прокидываем сразу в папку /var/www/html
docker run -d -v $pwd:/var/www/html php:7.3.3-apache ls /app


Редактирование файлов в phpstorm (может быть запрещено так как докер меняет владельца файлов)
надо изменить права для текущей папки с рута контейнера  на пользователя хоста
sudo chown -R orion:orion .


Создать свой image на основе Dockerfile
docker build . -t myproject/app




Связывание нескольких контейнеров (c помощью флага --link)
Именованый контейнер Mysql
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=sandbox -d mysql:5.7.25
Новый контейнер на основе файла Dockerfile со связью с предыдущим контейнером
docker run -d --link some-mysql -p 8080:80 -v $PWD:/var/www/html myproject/app


проверяем соединение в index.php
$dbh = new PDO('mysql:host=some-mysql;dbname=sandbox', 'root', 'secret');


в браузере на localhost:8080 была ошибка SQLSTATE[HY000] [2002] Connection refused  так как походу не щапущен сам сервис mysql? чтобы запустить надо в контейнере some=mysql запустить bash и команду
root@3bb3b9152662:/# mysql -u root -p
после ввода пароля secret все будет ок в браузере


Связывание контейнеров. Создание сети вручную (Создав сеть, и без связывания параметром --link)
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=sandbox -d mysql:5.7.25
docker run -d -p 8080:80 -v $PWD:/var/www/html myproject/app
Создаем с именем sandboxnet типа bridge
docker network create -d bridge sandboxnet
Алиас httpd-php (название внутри сети) для контейнера с именем которое докер создал
docker network connect --alias httpd-php sandboxnet romantic_volhard
В эту же сеть засовываем контейнер mysql 
docker network connect --alias some-mysql sandboxnet some-mysql


docker-compose
Переходим в папку docker-compose и выполняем
docker-compose up -d


