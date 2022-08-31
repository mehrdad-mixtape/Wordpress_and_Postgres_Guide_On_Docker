# Wordpress & PostgresSql Guide on Docker and Other Platforms!
<p align="left">
  <a href="https://skillicons.dev">
    <img src="https://skillicons.dev/icons?i=postgres,wordpress,docker" />
  </a>
</p>

## Simple way! step by step:

How to connect **PostgresSql** to **Wordpress** insted of MySql on `Docker-container` or `Other platforms`?

### Notice: All steps are implemented with examples and you can change anything you want depending on your needs! (*options*, *exposed ports*, *ip address* and etc...)

### Warning!: Don't skip any step!

1. **Pull images**:
    - WordPress:
        ```bash
        sudo pull wordpress
        ```
    - Postgres:
        ```bash
        sudo pull postgres
        ```

2. **Make Your Containers**:
    ```bash
    sudo docker container run --name webhost --publish 8085:80 --detach wordpress
    sudo docker container run --name wedatabase --env=POSTGRES_PASSWORD=1998mix$ --detach postgres
    ```
    - wordpress container name: **webhost**
    - postgres container name: **wedatabase**


![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index1.png)

3. **Get A shell from wedatabase**
    ```bash
    sudo docker container exec -it wedatabase bash
    ```
    1. login with ***postgres*** user
    2. get ***psql*** shell
    3. create database
    4. create superuser
    5. get high privilege to superuser
        ```bash
        sudo su - postgres
        psql
        create database wordpress_pgsql;
        create user admin with password 'admin';
        grant all privileges on database wordpress_pgsql to admin;
        \q
        ```
        - exit shell

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index2.png)

4. **Clone the plugin pg4wp**
    ```bash
    git clone https://github.com/kevinoid/postgresql-for-wordpress.git
    ```

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index3.png)

5. **Copy plugin to webhost and get a shell**
    ```bash
    sudo docker container cp postgresql-for-wordpress/ webhost:/
    sudo docker container exec -it webhost bash
    ```

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index4.png)

6. **Copy the pg4wp to var/www/html/wp-content/plugins**
    ```bash
    cp -rf /postgresql-for-wordpress/pg4wp/ /var/www/html/wp-content/plugins/
    ```

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index5.png)

7. **Copy the pg4wp/db.php to /var/www/html/wp-content/**
    1. Go to **/var/www/html/wp-content/**
    ```bash
    cd /var/www/html/wp-content/
    ```
    2. Copy ***db.php*** from **/var/www/html/wp-content/plugins/pg4wp/** to **/var/www/html/wp-content/**
    ```bash
    cp /var/www/html/wp-content/plugins/pg4wp/db.php /var/www/html/wp-content/
    ```

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index6.png)

8. **Update the repositories and install vim or nano editor**
    - I install vim :)
    ```bash
    apt update && apt install vim -y
    ```

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index7.png)

9. **Get Copy wp-config-sample.php and rename it to wp-config.php**
    ```bash
    cp /var/www/html/wp-content/wp-config-sample.php /var/www/html/wp-content/wp-config.php
    ```

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index8.png)

10. **Edit wp-config.php**

    **Notice: If you aren't using docker, don't change the DB_HOST**

    ```bash
    vim /var/www/html/wp-content/wp-config.php

    [...]
    define( 'DB_NAME', 'wordpress_pgsql' );
    define( 'DB_USER', 'admin' );
    define( 'DB_PASSWORD', 'admin' );
    define( 'DB_HOST', '172.17.0.3' );
    [...]
    ```
    - Find ip address of wedatabase:
        ```bash
        sudo docker container inspect --format '{{ .NetworkSettings.IPAddress }}' wedatabase
        172.17.0.3
        ```

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index9.png)

11. **Install php-pgsql Plugin**
    ```bash
    apt-get update \
    && apt-get install -y libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql
    ```
    - exit shell

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index10.png)

12. **Restart the webhost container**
    ```bash
    sudo docker container restart webhost
    ```

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index11.png)

## Congratulations!
    - Find your local ip address:
    - My local IPAddress: 192.168.1.200
    - Open you web-browser and go to LOCAL_IP_ADDRESS:8085

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index12.png)

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index13.png)

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index14.png)

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index15.png)

![image](https://github.com/mehrdad-mixtape/Wordpress_and_Postgres_Guide/blob/master/index16.png)

### Resources:
[Docker Form](https://forums.docker.com/t/your-php-installation-appears-to-be-missing-the-postgresql-extension-which-is-required-by-wordpress-with-pg4wp/74117/3)

[hevodata.com](https://hevodata.com/learn/wordpress-postgresql/)