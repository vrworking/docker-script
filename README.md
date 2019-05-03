# docker-script
my docker script, collect from web

## PHP 5.6.40 Official ?

[How to install more PHP extensions](https://github.com/docker-library/docs/tree/master/php#how-to-install-more-php-extensions)

```sh 
docker pull php:5.6-fpm # Debian Jessie(344MB)

docker pull php:5.6-fpm-alpine # alpine(54.5MB)

# 登录进去可查看安装的模块

docker run -it --rm php:5.6-fpm /bin/sh

docker run -it --rm php:5.6-fpm-alpine /bin/sh
```

* php -m  

```
Core
ctype
curl
date
dom
ereg
fileinfo
filter
ftp
hash
iconv
json
libxml
mbstring
mhash
mysqlnd
openssl
pcre
PDO
pdo_sqlite
Phar
posix
readline
Reflection
session
SimpleXML
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zlib
```

如果需要安装相应的扩展，可以在 Dockerfile 中运行官方内置的 docker-php-ext-install 命令安装  

```dockerfile
FROM php:5.6-fpm

# 复制php和php-fpm配置文件到相应目录
COPY php.ini-development /usr/local/etc/php/php.ini
COPY php-fpm.conf /usr/local/etc/php-fpm.conf

# 安装扩展所需要的依赖、安装iconv\mycrypt\gd扩展
RUN apt-get update && \
  apt-get install -y \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  libmcrypt-dev \
  libpng12-dev && \
  docker-php-ext-install iconv mcrypt && \
  docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && \
  docker-php-ext-install gd

ENV PHPREDIS_VERSION 2.2.7
# 安装redis扩展
RUN curl -L -o /tmp/redis.tar.gz "https://github.com/phpredis/phpredis/archive/$PHPREDIS_VERSION.tar.gz" && \
  tar xfz /tmp/redis.tar.gz && \
  rm -r /tmp/redis.tar.gz && \
  mv phpredis-$PHPREDIS_VERSION /usr/src/php/ext/redis && \
  docker-php-ext-install redis

WORKDIR /opt

# RUN usermod -u 1000 www-data

VOLUME ["/opt"]

CMD ["php-fpm"]
```

php 7.x install imagick

```dockerfile
FROM php:7.0.3-fpm

# Install Imagick.
RUN apt-get-update && \
  apt-get install -y libmagickwand-dev && \
  pecl install imagick && \
  docker-php-ext-enable imagick && \
  rm -r /var/lib/apt/lists/*
```

```dockerfile 
FROM php:7.0-fpm-alpine

MAINTAINER Florian GIRARDEY <fgirardey@groupeforum.pro>

# install the PHP extensions we need
RUN apk add --update freetype-dev libpng-dev libjpeg-turbo-dev libxml2-dev autoconf g++ imagemagick-dev libtool make \
    && docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install opcache \
    && docker-php-ext-install soap \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && apk del autoconf g++ libtool make \
    && rm -rf /tmp/* /var/cache/apk/*
```



## Nginx Official

![](https://raw.githubusercontent.com/docker-library/docs/01c12653951b2fe592c1f93a13b4e289ada0e3a1/nginx/logo.png)

[Dockerfile](https://github.com/nginxinc/docker-nginx/blob/9a052e07b2c283df9960375ee40be50c5c462a7e/stable/alpine/Dockerfile)

```sh 
docker pull nginx:1.16.0-alpine

docker run --name some-nginx -v /some/content:/usr/share/nginx/html:ro -d nginx

docker run --name my-custom-nginx-container -v /host/path/nginx.conf:/etc/nginx/nginx.conf:ro -d nginx
```

* Using environment variables in nginx configuration  

```docker-compose.yml
web:
  image: nginx
  volumes:
   - ./mysite.template:/etc/nginx/conf.d/mysite.template
  ports:
   - "8080:80"
  environment:
   - NGINX_HOST=foobar.com
   - NGINX_PORT=80
  command: /bin/bash -c "envsubst &lt; /etc/nginx/conf.d/mysite.template &gt; /etc/nginx/conf.d/default.conf &amp;&amp; exec nginx -g 'daemon off;'"
```
The mysite.template file may then contain variable references like this:

> listen ${NGINX_PORT};


## Tomcat Official

![](https://d1q6f0aelx0por.cloudfront.net/product-logos/56c8514a-eb15-4cc5-8e5e-3b89e7a408fa-tomcat.png)

[Dockerfile](https://github.com/docker-library/tomcat/blob/2d96f919237f0211d39409da5b2d0de7e3d8733b/8.5/jre8-alpine/Dockerfile)

```sh 
docker pull tomcat:8.5.40-jre8-alpine

docker run -it --rm -p 8888:8080 tomcat:8.0
```

The default Tomcat environment in the image for versions 7 and 8 is: 

```
CATALINA_BASE:   /usr/local/tomcat
CATALINA_HOME:   /usr/local/tomcat
CATALINA_TMPDIR: /usr/local/tomcat/temp
JRE_HOME:        /usr
CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
```

## jenkins Official

![](https://d1q6f0aelx0por.cloudfront.net/product-logos/f5326186-8ae7-425c-a78d-7192dabf75be-jenkins.png)

[Dockerfile](https://github.com/jenkinsci/jenkins-ci.org-docker/blob/c2d6f2122fa03c437e139a317b7fe5b9547fe49e/Dockerfile)

```sh 
docker pull jenkins:2.60.3-alpine

docker run -p 8080:8080 -p 50000:50000 jenkins

docker run -p 8080:8080 -p 50000:50000 -v /your/home:/var/jenkins_home jenkins

```

## rabbitmq Official

![](https://d1q6f0aelx0por.cloudfront.net/product-logos/d50b96ee-666d-43ce-8d91-d0cbb6f93ffb-rabbitmq.png)

[具体用法](https://hub.docker.com/_/rabbitmq)

```sh 
docker pull rabbitmq:3.7.14-alpine
docker pull rabbitmq:3.7.14-management-alpine
```

