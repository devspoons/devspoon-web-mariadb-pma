# devspoon-web-mariadb-pma
This solution contains mariadb and phpmyadmin and is based on the [devspoon-web] repository. This repository was created to perform full integration tests on one server.

## Features

* **Support to make configuration files for each service(conf, yml etc)** : Using shell script, you can easily make and manage the configuration files required for nginx, php, dockerfile, etc. with only the information required by the user's keyboard.

* **Efficiently  dockerfile configuration for development and service operation** : The log folder is interlocked by "volumes" in docker-compose.yml so that user can can be tracked problems even when the docker container is stopped. Webroot, nginx config, etc. are frequently modified during development so these are interlocked by "volumes" 

* **Provide reverse proxy function** : Multiple web and app services can be provided through one nginx with php or python and services can be provided simultaneously. A shell script is provided to easily create a proxy config file so that it can be integrated with the web UI of other services.

* **Provides easy distributed service operation method** : You can use multiple web servers through proxy, and you can use multiple app servers on one web server. (How to set load balancing will be supported in the future)

## Considerations

* **Development-oriented docker service** : This open source is designed for focused on development-oriented rather than perfect docker container distribution and is suitable for startups or new service development teams with frequent initial modifications and tests.

## Install & Run

1. Make webroot folder
      ```
      User have to make new folder under www path

      Example : /www/home_test
      ```


2. Make a conf file of nginx
 
### PHP service
***

   * **PHP service installation [nginx for php]**
      ```
      In config/web-server/php
      There are 2 shell script
      Use "chmod +x xxxx.sh" command, you activate shell script and run. then it make conf file
      nginx's a conf file will be in conf.d folder
      if your webroot path has sub-level, input type must be following as "\\/www\\/shop\\/shop_kings
      ```

      ```
      Shell script required informations like bellow
      webroot : ex -> shop_kings
      domain : ex -> xxxx.com
      portnumber : ex -> 80
      appname : ex -> php-app (user must be use "container name" referenced in docker-compose.yml file)
      serviceport : ex -> 9000 (php application service port)
      filename : ex -> xxxx (it's the name for nginx's conf file)
      ```

  
   * **PHP service installation [php application]**

      ```
      In config/app-server/php
      There are 1 shell script
      Use "chmod +x xxxx.sh" command, you activate shell script and run.sh then it make conf file
      nginx's a conf file will be in pool.d folder
      ```

   * **Run docker-compose.yml**
      ```
      Get move to compose/nginx_php
      Execute docker-compose.yml using "docker-compose up -d" command
      ```
    
### Gunicorn service
***
   * **Gunicorn service installation [nginx for gunicorn]**
      ```
      In config/web-server/gunicorn
      There are 2 shell script
      Use "chmod +x xxxx.sh" command, you activate shell script and run.sh then it make conf file
      nginx's a conf file will be in conf.d folder
      * if your webroot path has sub-level, input type must be following as "\\/www\\/shop\\/shop_kings
      ```

      ```
      Shell script required informations like bellow
      webroot : ex -> shop_kings 
      domain : ex -> xxxx.com
      portnumber : ex -> 80
      appname : ex -> gunicorn-app (user must be use "container name" referenced in docker-compose.yml file)
      serviceport : ex -> 8000 (gunicorn application service port)
      filename : ex -> xxxx (it's the name for nginx's conf file)
      ```

   * **Gunicorn service installation [gunicorn application]**

      ```
      * If user want to use config.py, user have to modify run.sh file in docker/gunicorn/
      In docker/gunicorn/

      Dockerfile required run.sh file to start gunicorn service in a container 
      There are 2 shell script, make_run.sh and run.sh in /docker/gunicorn

      if you want to use sample project django_test in /www/py37, you can use run.sh.
      if you want to use new project, you must make run.sh using make_run.sh
      * when you input the path, considered "\\/www\\/shop\\/shop_kings
      ```

   * **Run docker-compose.yml**
      ```
      Get move to compose/nginx_gunicorn
      Execute docker-compose.yml using "docker-compose up -d" command
      ```

### UWSGI service
***
   * **UWSGI service installation [nginx for uwsgi]**
      ```
      In config/web-server/uwsgi
      There are 2 shell script
      Use "chmod +x xxxx.sh" command, you activate shell script and run.sh then it make conf file
      nginx's a conf file will be in conf.d folder
      * if your webroot path has sub-level, input type must be following as "\\/www\\/shop\\/shop_kings
      ```

      ```
      Shell script required informations like bellow
      webroot : ex -> shop_kings 
      domain : ex -> xxxx.com
      portnumber : ex -> 80
      appname : ex -> uwsgi-app (user must be use "container name" referenced in docker-compose.yml file)
      serviceport : ex -> 8000 (uwsgi application service port)
      filename : ex -> xxxx (it's the name for nginx's conf file)
      ```

   * **UWSGI service installation [uwsgi application]**

      ```
      In config/app-server/uwsgi
      There are a file of uwsgi_conf.sh
      you can make uwsgi.ini using this shell script file
      ```

      ```
      Dockerfile required run.sh file to start gunicorn service in a container 
      There are 2 shell script, make_run.sh and run.sh in /docker/uwsgi

      if you want to use sample project django_test in /www/py37, you can use run.sh.
      if you want to use new project, you must make run.sh using make_run.sh
      * when you input the path, considered "\\/www\\/shop\\/shop_kings
      ```


   * **Run docker-compose.yml**
      ```
      Get move to compose/nginx_uwsgi
      Execute docker-compose.yml using "docker-compose up -d" command
      ```
***

## How to develop based on working server

* User can access using defined folders in docker-compose.yml

      Example -> nginx container has volumes like below that
            
      /www
      /script/
      /etc/nginx/conf.d/
      /etc/nginx/nginx.conf
      /etc/nginx/uwsgi_params
      /ssl/
      /log

      If user run containers at same server, can update code and move files directly from local server folder to container folder.       

* If user use firewall, have to add required port number (refer each docker-compose.yml files)

      Example

      ufw allow 80/tcp
      ufw allow 3306/tcp

## Setting up HTTPS on a web server
* This step requires running http nginx server

      1. There are letsencrypt.sh shell script file in script folder and it interlocked by volumes.
      So user can access script file in a nginx container.

      2. Use "docker exec -it <nginx container name> bash" command, user can get docker inside.
      
      3. Run letsencrypt.sh and insert informations such as webroot, domain, e-mail etc.
         This script make ssl-key and make crontab schedule automatically

      4. Using "exit" command user can get off from container
      
      5. Run nginx_https_conf.sh existing. it make conf file under config/web-server/<service>

      6. User have to remove http conf file in config/web-server/<service>/conf.d/

      7. Run "docker-compose up" command in the compose folder


## Community

* **Personal Website :** Owner's personam website is [devspoon.com]
* **Github.io :** Ther are more detail guide [devspoon.github.io]

## Demos

* **[youtube]** - Preparing
* **[inflearn]** - Demos for Devspoon features and how to use the devspoon's open-source

## Partners and Users

* Lim Do-Hyun Owner Developer/project Manager, bluebamus@gmail.com  
Personal github.io : [bluebamus.github.io]

* 임도현 Owner 개발자/기획자, bluebamus@gmail.com  
개인 github.io 사이트 : [bluebamus.github.io]

<!-- Markdown link & img dfn's -->
[devspoon-web]: https://github.com/devspoons/devspoon-web
[devspoon.github.io]: https://github.com/devspoons/devspoon.github.io
[wiki]: https://github.com/yourname/yourproject/wiki
[youtube]: https://www.youtube.com/
[inflearn]: https://www.inflearn.com/
[bluebamus.github.io]: bluebamus.github.io
[devspoons.github.io]: devspoons.github.io