# Zabbix deployment guide

## Intro

This is a basic explanation that I wrote while trying to deploy zabbix.
I suggest that you have the relevant zabbix documentation page available to you.
Refer to https://www.zabbix.com/documentation/3.0/manual/installation/containers

Some of the issues I ran into were compatibility issues, like MySQL version. Make sure that you use exact versions, not latest.

## MySQL server

### Start the container

Run the following command

```
docker run --restart=always -v /home/dn/mysql_volume:/var/lib/mysql --name=zabbix-mysql -e MYSQL_DATABASE=zabbix -d mysql/mysql-server:5.7 --character-set-server=utf8 --collation-server=utf8_bin
```

Note that we need to use the mysql native password plugin to allow zabbix to connect.
Refer to https://github.com/docker-library/mysql/issues/419 for details.


### Change the root password

* Run the command `docker logs zabbix-mysql 2>&1 | grep GENERATED` to get the password.
* Run `docker exec -it zabbix-mysql mysql -uroot -p` and enter the generated command.
* Set the new root password


### Create a new user for zabbix

Note that we need to grant permissions for the zabbix user to access the MySQL server from a different IP.
To simplify the flow in the guide we just use a wildcard, i.e %, for the host name.
We also use the mysql native password identification type since zabbix doesn't work with newer types.

```
CREATE USER 'zabbix'@'%' IDENTIFIED with mysql_native_password BY 'zabbix';
GRANT ALL PRIVILEGES ON *.* TO 'zabbix'@'%';
```


### Create a database for zabbix

```
create database zabbix character set utf8 collate utf8_bin;
```


### Create initial tables in the database

For some reason the zabbix container doesn't come with the required sql files to initialize the database.
To get them you will need to download the matching zabbix deb file, install it and run the sql file on your database.

```
# wget http://repo.zabbix.com/zabbix/3.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.0-2+trusty_all.deb
# dpkg -i zabbix-release_3.0-2+trusty_all.deb
# apt update
# apt install zabbix-server-mysql zabbix-frontend-php zabbix-agent
```

Note that more information can be found on the zabbix guide https://www.zabbix.com/download?zabbix=3.0&os_distribution=ubuntu&os_version=trusty&db=MySQL

## Zabbix server

### Start the container

```
docker run \
    -p 10051:10051 \
    -p 10080:80 \
    --link zabbix-mysql \
    --link zabbix-java-gateway \
    --name zabbix-server \
    -e DB_SERVER_HOST=zabbix-mysql \
    -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
    -e MYSQL_USER=zabbix \
    -e MYSQL_PASSWORD=zabbix \
    -e ZBX_SERVER_HOST=100.64.1.44 \
    -e ZBX_SERVER_PORT=10051 \
    -e ZBX_JAVAGATEWAY_ENABLE=true \
    -d \
    --restart=always \
    zabbix/zabbix-web-nginx-mysql:ubuntu-3.0.17
```

Note that we are using version ubuntu-3.0.17 since other versions simply didn't work when I wrote this guide...

### Configuration

The default username/password is Admin/zabbix.


## Agents

```
docker run --name dn21-zabbix-agent --privileged --restart=always -e ZBX_SERVER_HOST=100.64.1.44 -d zabbix/zabbix-agent:ubuntu-3.0.17
```
