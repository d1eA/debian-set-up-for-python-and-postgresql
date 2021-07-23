# РАЗВЕРТЫВАНИЕ Python и POSTGRESQL

## Прокидываем ключи:

- Заходим в puTTYgen и создаём ssh-ключи.
- После этого заходим через puTTY на сервер под root`ом.
- Вводим:

```
mkdir .ssh;\
cd .ssh;\
`ano authorized_keys;\
```

- В открывшемся окне вводим публичный ssh-ключ.

- В PuTTY во вкладке Connection=>SSH=>Auth прописываем путь до закрытого ssh-ключа. 

## Скачиваем зависимости для Python и доп. инструменты (vim, tmux, htop etc.):

````
apt-get install -y vim tmux htop git wget unzip zip gcc build-essential make sudo;\

apt-get install -y tree redis-server zlib1g-dev libbz2-dev libreadline-dev llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev liblzma-dev python3-dev python-imaging python3-lxml libxslt-dev python-libxml2 python-libxslt1 libffi-dev libssl-dev python-dev gnumeric libsqlite3-dev libpq-dev libxml2-dev libxslt1-dev libjpeg-dev libfreetype6-dev libcurl4-openssl-dev supervisor
````

## Создаём пользователя slaves и прокидываем те же ssh-ключи:

```
adduser slaves;\
usermod -aG sudo slaves;\
su slaves;\
mkdir .ssh;\
cd .ssh;\
sudo nano authorized_keys;\
```

## Собираем Python-3.9.0:

```
wget https://slaves.python.org/ftp/python/3.9.0/Python-3.9.0.tgz ; \
tar xvf Python-3.9.* ; \
cd Python-3.9.0 ; \
mkdir ~/.python ; \
./configure --enable-optimizations --with-zlib --prefix=/home/slaves/.python ; \
make -j2 ; \
sudo make altinstall
```

### Обновляем PIP и удаляем уже не нужные папки:

```
sudo /home/slaves/.python/bin/python3.9 -m pip install -U pip
```

```
cd;\
sudo rm -rf Python-3.9.0
```

```
sudo rm Python-3.9.0.tgz
```
# Сборка POSTGRESQL:

- Создаем пользователя postgres:

```
sudo adduser postgres
```

```
sudo usermod -aG sudo postgres
``` 

```
sudo apt update
```

- Скачиваем и устанавливаем зависимости и сам POSYGRESQL

```
sudo apt -y install gnupg2
```

```
wget --quiet -O - https://slaves.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -;\

echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
```

```
sudo apt update
```

```
sudo apt -y install postgresql-12 postgresql-client-12
```

### Проверяем POSTGRESQL:

```
systemctl status postgresql.service;\
systemctl status postgresql@12-main.service;\
systemctl is-enabled postgresql
```

## Создаем пользователя и базу данных:
- Переключаемся на пользователся postgres:

```
sudo su postgres
```

- Создаем пользователя (в бд) с именем postgres и паролем password:

```
psql -c "alter user postgres with password 'password'"
```

- Создаём бд с именем database:

```
psql
```

```
CREATE DATABASE database
```

- Создаем пользователя user с паролем passw:

```
CREATE USER user WITH ENCRYPTED PASSWORD 'passw'
```
- Даём пользователю user полный доступ к базе денных database и выходим из диалогового окна:

```
GRANT ALL PRIVILEGES ON DATABASE user to datebase
```

```
\q
```

- Переключаемя на пользователя slaves и пытаемся подключиться к базе данных database:

```
exit
```

```
psql -h localhost -U dzbot dz_db
```

- Выходим из диалогового окна:

```
\q
```