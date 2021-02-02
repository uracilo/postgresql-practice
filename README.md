# CREATE DB on EC2


```sh
sudo apt-get update -y && sudo apt-get upgrade -y
sudo apt install postgresql -y

sudo su postgres
psql -U postgres -c "CREATE ROLE ubuntu;"
psql -U postgres -c "ALTER ROLE  ubuntu  WITH LOGIN;"
psql -U postgres -c "ALTER USER  ubuntu  CREATEDB;"
psql -U postgres -c "ALTER USER  ubuntu  WITH PASSWORD 'ubuntu';"
exit

# bind 5432 to the public IP so we can access it from outside the machine
# first find the config file
sudo find / -name "postgresql.conf"
sudo nano /etc/postgresql/12/main/postgresql.conf
# edit the config file to allow listen addresses beyond localhost by adding/modifying this line: 
listen_addresses = '*'
# find the hba conf
sudo find / -name "pg_hba.conf"
sudo nano /etc/postgresql/12/main/pg_hba.conf
# add these 2 lines to the end of that file
host    all             all              0.0.0.0/0                       md5
host    all             all              ::/0                            md5
```

# restart the server
sudo systemctl restart postgresql

# POSTGRESQL con Docker API FLASK 
## Levantar el servicio 

```sh
docker build  . -t apipostgresql
docker run -it -p 5000:5000 --name api-flask  apipostgresql
```

### Comando inicial de la base de datos

1.- Obtener el id de la imagen de rds-aws_api-flask 
```sh
docker exec  -it api-flask bash 
```

2.- Crear la base de datos y crear los datos iniciales
```sh
flask db_create && flask db_seed 
```

### Revisar el API

- Ver health
```sh
curl localhost:5000/
```
- GET all quotes
```sh
curl --location --request GET 'http://0.0.0.0:5000/quotes' --data-raw ''
```
- POST a quote

```sh
curl --location --request POST 'http://0.0.0.0:5000/add_quote' \
--form 'quote_desc=D'\''oh!' \
--form 'quote_type=Motivation' \
--form 'author=Homero Simpson'
```
- PUT a quote

```sh
curl --location --request PUT 'http://0.0.0.0:5000/update_quote/6' \
--form 'quote_desc=D'\''oh!' \
--form 'quote_type=Motivation' \
--form 'author=Homero Jay  Simpson'
```
- POST a quote

```sh
curl --location --request POST 'http://0.0.0.0:5000/add_quote' \
--form 'quote_desc=Tienes el micronfono apagado' \
--form 'quote_type=Motivation' \
--form 'author=Benjamin'
```
- DELETE a quote

```sh
curl --location --request DELETE 'http://0.0.0.0:5000/remove_quote/7'
```
