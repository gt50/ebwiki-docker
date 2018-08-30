FROM ubuntu:18.04

ENV RUBY_VERSION 2.5.1
ENV RAILS_VERSION 4.2.10
ARG BLACK_OPS_DATABASE_PASSWORD=ebwiki
ARG AWS_ACCESS_KEY_ID=accessKey1
ARG AWS_SECRET_KEY_ID=verySecretKey1

RUN apt-get update && apt-get dist-upgrade -y

#Install Ruby https://github.com/rvm/ubuntu_rvm
RUN apt-get install -y software-properties-common && \
	apt-add-repository -y ppa:rael-gc/rvm && \
	apt-get update && \
	apt-get install -y rvm && \
	/usr/share/rvm/bin/rvm install $RUBY_VERSION

#Install Rails 
RUN /bin/bash -c "source /usr/share/rvm/scripts/rvm && \
	rvm use $RUBY_VERSION --default && \
	gem install rails -v $RAILS_VERSION && \
 	gem install fakes3 && \
	gem install bundler" 

#Install Postgresql
RUN export DEBIAN_FRONTEND=noninteractive && \ 
	apt-get install -y tzdata && \
	apt-get install -y postgresql

USER postgres

RUN /etc/init.d/postgresql start && \
	psql --command "CREATE USER blackops WITH PASSWORD '${BLACK_OPS_DATABASE_PASSWORD}';" &&\
	psql --command "ALTER USER blackops CREATEDB;"

USER root

#Install Node.js
RUN apt-get install -y nodejs

#Install JRE
RUN apt-get install -y openjdk-8-jre

#Install Nginx
RUN apt-get install -y nginx
COPY ./files/nginx.conf /etc/nginx/sites-available/default
RUN /etc/init.d/nginx reload

#Install Elasticsearch
RUN apt-get install -y wget gnupg
RUN wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | apt-key add -
RUN apt-get install -y apt-transport-https
RUN echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | tee -a /etc/apt/sources.list.d/elastic-6.x.list
RUN apt-get update && apt-get install -y elasticsearch

#Install redis
RUN apt-get install -y redis-server

#Install Dependancies for fog gem
RUN apt-get install -y build-essential libcurl4-openssl-dev
RUN apt-get install -y apt-transport-https build-essential curl gcc \
    git-core libcurl4-openssl-dev libpq-dev libreadline-dev \
    libsqlite3-dev libssl-dev libxml2-dev libxslt-dev \
    libyaml-dev sqlite3  zlib1g-dev

#Install ebwiki
RUN apt-get install -y git
RUN git clone https://github.com/EBWiki/EBWiki.git 

#Uncomment database.yml lines
RUN sed -i 's/#username:/username:/g' /EBWiki/config/database.yml && \
	sed -i 's/#password: /password: /g' /EBWiki/config/database.yml && \
	sed -i 's/#host: /host: /g' /EBWiki/config/database.yml && \
	sed -i 's/#port: /port: /g' /EBWiki/config/database.yml 

#Bundle install and Local Database Setup
#RUN /bin/bash -l -c "cd /EBWiki && \
#        source /usr/share/rvm/scripts/rvm && \
#	/etc/init.d/postgresql start && \
#	export BLACKOPS_DATABASE_PASSWORD='${BLACK_OPS_DATABASE_PASSWORD}' && \
#	export AWS_ACCESS_KEY_ID='${AWS_ACCESS_KEY_ID}' && \
#	export AWS_SECRET_KEY_ID='${AWS_SECRET_KEY_ID}' && \
#        bundle install && \
#	rake db:create && \
#	rake db:migrate && \
#	rake db:seed"	


