# docker_php
php7.4 php8.1 + nginx + mysql and phpmyadmin

# Installation
* 1.Folder: php74, php81 
* 2.Open (1.) 
*     docker compose build  
*     docker compose up -d
* 3.Copy laravel Project to (1.) or PHP Original Web to (1.)/pulic
* 4.if you want to run composer
  Login to Container
  -     docker compose exec -it php /bin/bash
  -     cd /var/www/app.dev
  -     composer install
