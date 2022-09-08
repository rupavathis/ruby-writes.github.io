---
layout: post
title: Creating a new Rails app with docker 
---

In this tutorial, we will learn how to create a new rails app using docker. To speak on a broader sense, we will not have rails installed in our host machine rather we run new rails app within a docker container, however, we don't need to get into docker container everytime we run the app except for the first time or for debugging purposes. Let's get into the tutorial to understand how we reach the mentioned goals. 

## Step 1: Create project directory
Create your project directory ```railsapp```. Inside the new folder, create a Dockerfile and pull ```ruby``` image and run required installations. On top of it, install ```rails```. 

```
    FROM ruby:3.1
    RUN apt update -qq && apt install -y nodejs postgresql-client
    RUN gem install rails -v '7.0.3.1'
```
Let's build the docker image with the command ```docker build -t railsapp:latest .```    

## Step 2: Create new rails app with rails-new    
Now let's run the docker and bash within the docker with the below command, 
```sh
    docker run --rm -it -v $(pwd):/project railsapp:latest /bin/bash
```
Inside the docker, run the below commands to create a new ```rails``` app with a ```postgresql``` database. Once the new rails app is created, move the folder ```railsapp``` within our project directory. Finally exit the docker terminal. 

```sh
    rails new railsapp --api --database=postgresql --skip-bundle
    mv railsapp/{*,.*} /project/
    exit
```

## Step 3: Update Dockerfile from the host

Docker, by default creates the project folder with the ```root``` permissions. In order to change those permissions, run ```sudo chown -R $USER:$USER .``` within the project folder.  

Let's update the ```config/database.yml``` file

```
    Add the following in config/database.yml file
    default: &default
    # other stuff
    # adapter: ...
    # encoding: ...
    # pool: ...
    host: db
    username: postgres
    password: password
```

Now, update the Dockerfile with the below lines.

``` 
    FROM ruby:3.1
    RUN apt update -qq && apt install -y nodejs postgresql-client
    RUN gem install rails -v '7.0.3.1'

    ARG APP=/railsapp
    WORKDIR ${APP}
    ADD Gemfile ${APP}/
    RUN bundle install
```

Let's breakdown the updated lines. The app folder is set to the argument ```APP``` and make it the working directory ```WORKDIR```. Now, add the ```gemfile``` within the app folder and finally run the bundle to install. 

## Step 4: Create docker compose file
Create a docker-compose.yml file and copy the below lines of code.

```
    version: "2.2"
    services:
    db:
        image: postgres
    web:
        build: .
        command: bundle exec rails s -p 3000 -b '0.0.0.0'
        ports:
        - "3000:3000"
        depends_on:
        - db
        volumes:
        - ./:/railsapp

```

We have two services, ```db``` and ```web```. For the db service, pull ```postgres``` image from the docker hub. For the web service, mention the required port and dependencies. 

## Step 5: Run docker compose

Run the below commands

```sh
    docker compose build
    docker compose up
```
While the ```docker compose``` is up and running, open a new terminal and run create the ```db```

```sh
    docker compose run web rails db:create
```

Now, you can view your app from ```http://localhost:3000```

# Adding env file in rails app

It is a [good practice](https://12factor.net/) to keep the configuration in a separate file inside the codebase. Fortunately, the variables specified in docker-compose.yml file, without no extra setup, will be subsituted with the variables specified inside the .env file (if they are not present in the environment variables already).

## Steps to add .env file
1. Add a new line .env at the end of .gitignore file if it is not present already. This .env file which we will create in the next step contains all the secrets and hence is added to .gitignore before creation!

2. Add a new .env file with the following contents:

    ```
    RAILS_APPNAME=railsapp
    RAILS_DEMO_POSTGRES_USERNAME=railsapp
    RAILS_DEMO_POSTGRES_PASSWORD=password
    RAILS_DEMO_POSTGRES_HOST=db
    ```

3. [postgres](https://hub.docker.com/_/postgres) docker image allows creating specific roles with the specified password using the [entrypoint](https://github.com/docker-library/postgres/blob/master/docker-entrypoint.sh) script. Add under ```db``` in ```docker-compose.yml```,

    ```
    services:
        db:
            environment:
                - POSTGRES_USER=${RAILS_DEMO_POSTGRES_USERNAME}
                - POSTGRES_PASSWORD=${RAILS_DEMO_POSTGRES_PASSWORD}
    ```

4. All the environment variables inside .env file are required for our rails app and so, under ```web``` in ```docker-compose.yml``` add,

    ```
    services:
        web:
            env_file:
                - .env
    ```

5. Replace the hardcoded variables previously added in the ```database.yml``` file
    ```
    default: &default
    # other stuff
    # adapter: ...
    # encoding: ...
    # pool: ...
    host: <%= ENV["RAILS_DEMO_POSTGRES_HOST"] %>
    username: <%= ENV["RAILS_DEMO_POSTGRES_USERNAME"] %>
    password: <%= ENV["RAILS_DEMO_POSTGRES_PASSWORD"] %>
    ```
