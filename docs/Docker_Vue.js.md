# 〜DockerでVue.js環境を整える〜

## ■　Dockerの設定
### 1、Dockerディレクトリを作成
~~~
docker-rails/
  |-docker-compose.yml  #これはこの場にあったほうがいい  
  |-Gemfile  
  |-Gemfile.lock  
  |-docker/  
     |-mysql/  
     |  |-password.yml  #MySQLのパスワードはここに記述  
     |  |-volumes/       #このフォルダにMySQLのデータを永続化  
     |-rails/  
     |  |-Dockerfile    #Rails用のDockerfile  
~~~

### 2、Dockerfileを作成
./docker/rails/Dockerfile  
~~~
FROM ruby:2.5.1  

ENV LANG C.UTF-8  

# for MySQL  
# 必要なライブラリインストール  
RUN apt-get update -qq && apt-get install -y   build-essential libpq-dev mysql-client  

# yarnパッケージ管理ツールインストール
RUN apt-get install -y curl apt-transport-https wget && \  
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \  
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \  
    apt-get update && apt-get install -y yarn  

# Node.jsをインストール
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash - && \  
    apt-get install nodejs  

# Cannot find module node-sass対策  
RUN yarn add node-sass  

RUN gem install bundler  

WORKDIR /tmp
ADD Gemfile Gemfile
ADD Gemfile.lock Gemfile.lock
RUN bundle install

ENV APP_HOME /myapp
RUN mkdir -p $APP_HOME
WORKDIR $APP_HOME
ADD . $APP_HOME
~~~

### 3、docker-compose.ymlを作成
./docker-compose.yml
~~~
version: '2'
services:
  db:
    image: mysql:5.7.17
    ports:
      - "3306:3306"
    volumes:
      - ./docker/mysql/volumes:/var/lib/mysql  
    extends:
      file: ./docker/mysql/password.yml
      service: password
  web:
    build:
      context: .
      dockerfile: ./docker/rails/Dockerfile
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    ports:
      - "3000:3000"
    volumes:
      - .:/myapp
    environment:
      RAILS_ENV: development
    extends:
      file: ./docker/mysql/password.yml
      service: password
    depends_on:
      - db
~~~

### 4、MySQLのパスワードを設定
./docker/mysql/password.yml
~~~
version: '2'
services:
  password:
    environment:
      MYSQL_ROOT_PASSWORD: password
~~~

### 5、Gemfileを作成
./Gemfile
~~~
source 'http://rubygems.org'
gem 'rails', '5.2.1'
~~~

### 6、Gemfile.lockを作成
./Gemfile.lock
~~~
空ファイル
~~~

***

## ■　プロジェクトの作成
### 1、新規アプリを作成
~~~
$ docker-compose run --rm web rails new . --webpack=vue -f -d mysql  
$ docker-compose build
~~~

### 2、データベースに接続
config/database.yml
~~~
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: <%= ENV['MYSQL_ROOT_PASSWORD'] %>
  host: db  

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test
~~~

### 3、データベースを作成
~~~
$ docker-compose up -d  
$ docker exec -it <コンテナID> bash  
# rails db:create  
~~~
***
## ■　Railsの設定
### 1、コントローラーを作成
~~~
# rails g controller home index
~~~

### 2、ルートを設定
config/routes.rb
~~~
Rails.application.routes.draw do  
+ root to: 'home#index'
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end
~~~

### 3,Viewを空にする
app/views/home/index.html.erb
~~~
~~~

### 4、javascriptの呼び出し設定
app/views/layouts/application.html.erb
~~~
<!DOCTYPE html>
<html>
  <head>
    <title>RailsVueSandbox</title>
    <%= csrf_meta_tags %>

    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= yield %>
+    <%= javascript_pack_tag 'hello_vue' %>
  </body>
</html>
~~~

### 5、JSのコンパイル
~~~
$ bin/webpack
~~~

### 6、Hello World!を表示させる
http://localhost:3000/で `Hello World!` が表示される
