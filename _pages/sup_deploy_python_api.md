---
layout: post
title:  "How can I deploy a Python API with Sup?"
date:   2019-08-17 01:03:01 +0200
categories: DevOps
category: DevOps
description: This is a second post in the deployment series. Here we'll deploy a simple Python API as a docker-compose service, using Sup.
cover: "https://www.infrontfinance.com/media/1677/pythonlogo.png"
author: Vasil Vasilev
---

In our [previous post](/pages/how_to_deploy_with_sup) we covered how you can setup your Sup deployment environment.  
**Refer to the [repository](https://github.com/sp0x/SupExamplePythonAPI) to see the whole code for the API.**   
Now we'll deploy a simple hello world API with Sup, as a docker-compose service. 
First open up a bash shell(the one we set up in the previous post).

## The API Code
Our API is just a simple hello world API that consists of a single `/` route, which returns "Hello world".  
Check out the repository to get all the code.  
The API would listen on port **0.0.0.0:5000**. 

## Docker
We now need to create a Dockerfile and docker-compose.yml file.  
The Dockerfile describes your docker image for the API. The docker-compose.yml file describes the services that we'll be using.
Here's an example Dockerfile:
```dockerfile
FROM python:3.6-alpine
RUN mkdir /app
WORKDIR /app
ADD requirements.txt .
RUN pip install -r requirements.txt
ADD . .
CMD ["python", "__init__.py"]
```  
Here we're starting with an image based off of Python 3.6 using alpine linux, so our image is small in size.
We're making an `/app` directory and we're setting it as the working directory.  
Then we're adding all the requirements that our python API needs.  Refer to the [repository](https://github.com/sp0x/SupExamplePythonAPI) to see the whole code for the API.  
Finally we're copying all of our files into the image and setting the default command that's ran when we run our container as `python __init__.py`.  

## Docker-Compose

And an example docker-compose.yml file:
```yaml
version: "3"
volumes:
  db:

services:
  mysql:
    image: mysql:5.7.20
    restart: always
    volumes:
      - db:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: thisismypassword

  app:
    build:
      context: .
      # specified so that you can run `docker-compose push`
    image: your.registry.url
    restart: always
    ports:
      - 8089:5000
```
We're defining a `db` volume  which will persist the database for MySql.  
After that we have 2 services: `mysql` and `app`.  
The first one is just a simple mysql service that you can use in your API with a default password.
The second service is our API. It's built using our Dockerfile and it can be pushed to your registry, if you have one.  
The app service also exposes port **8089** which is connected to our python code's port **5000**.  
With this now done, we can finally start on the setup of our deployment file.

## Creating a Supfile.yml for our project
First we'll make a directory for our project, let's name it pyapi.  
Inside it I'll create a file named `Supfile.yml`.  
```yaml
version: 0.4

#The environment vars you'll use
env:
  NAME: flask_api
  IMAGE: netlyt/flask_api
  SERVICE_NAME: app
  HOST_PORT: 8089
  CONTAINER_PORT: 5000
  DIR: /tmp/$IMAGE

networks:
  # The staging directory
  prod:
    hosts:
      - <your ssh host>

commands:
  #These commands are just helpers, don't modify them
  ping:
    desc: Print uname and current date/time
    run: uname -a; date

  bash:
    desc: Interactive shell on all hosts
    stdin: true
    run: bash

  pre-build:
    desc: Prebuild tasks
    run: mkdir -p $DIR

  build:
    desc: Build Docker image from current directory, push to Docker Hub
    # local: sup $SUP_ENV -f ./builder/Supfile $SUP_NETWORK build
    upload:
      - src: ./
        dst: $DIR
        exclude: node_modules,.git
    script: ./scripts/docker-build.sh
    once: true
 
  pull:
    desc: Pull latest Docker image
    run: sudo docker-compose pull $SERVICE_NAME
    chdir: $DIR

  stop:
    desc: Stop Docker container
    run: sudo docker-compose stop $SERVICE_NAME || exit 0
    chdir: $DIR

  rm:
    desc: Remove Docker container
    run: sudo docker-compose rm -f $SERVICE_NAME || exit 0 
    
  run:
    desc: Run Docker container
    run: >
      sudo docker-compose up -d $SERVICE_NAME
    chdir: $DIR

  restart:
    desc: Restart Docker container
    run: sudo docker-compose restart $SERVICE_NAME || exit 0
    chdir: $DIR

  ps:
    desc: List running Docker containers
    run: sudo docker-compose ps
    chdir: $DIR

  logs:
    desc: Docker logs
    run: sudo docker-compose logs | grep $SERVICE_NAME
    chdir: $DIR

  tail-logs:
    desc: Tail Docker logs
    run: sudo docker-compose logs --tail=20 -f $SERVICE_NAME
    chdir: $DIR

  health:
    desc: Application health check
    run: curl localhost:$HOST_PORT

  slack-notify:
    desc: Notify Slack about new deployment
    local: >
      curl -X POST --data-urlencode 'payload={"channel": "#dev", "text": "['$SUP_NETWORK'] '$SUP_USER' deployed '$NAME'"}' \
        https://hooks.slack.com/services/ID


targets:
  #The deployment target command, it runs multiple commands one after another, ensuring the previous one returned 0
  deploy:
    - pre-build
    - build
    - run
    - ps
    - logs
    - health
    - slack-notify

```
This file describes how you're going to deploy your API and to which networks.  
We defined a few commands and a **deploy** target which is a collection of commands.  
To try out the file, run `sup prod ping`, it should print out the output of the ping command that we defined in the yml file.
Example output:
```bash
vasko@<host>:22 | Linux compute 4.9.0-8-amd64 #1 SMP Debian 4.9.144-3.1 (2019-02-19) x86_64 GNU/Linux
vasko@<host>:22 | Sun Aug 18 14:45:02 UTC 2019
```
To get a short help screen of all the commands run `sup prod` and you'll see this:
```text
Targets:
- deploy        pre-build push lf build run

Commands:
- ping          Print uname and current date/time
- bash          Interactive shell on all hosts
- pre-build     Prebuild tasks
- push          Push all files from this directory to remote host
- build         Build Docker image from current directory, push to Docker Hub
- pull          Pull latest Docker image
- stop          Stop Docker container
- rm            Remove Docker container
- run           Run Docker container
- restart       Restart Docker container
- ps            List running Docker containers
- logs          Docker logs
- tail-logs     Tail Docker logs
- health        Application health check
- lf            Corrects your line endings
- perms         Sets the permissions to the current user and a specific group
- slack-notify  Notify Slack about new deployment
```
A useful command is `bash` which opens an interactive bash shell to your host or `tail-logs` which starts tailing the logs for our `app` service that we defined in docker-compose.
Note that the beginning of the Supfile.yml file has an `env` section.  
The variables in that section are used in the deployment.  
If you have a team that'll also be working on the project, it's useful to set the $GROUP variable, then any member of that group on your instance can deploy.


To deploy our API we'll be using the `deploy` target.  
The target executes these actions:
- It creates the directory in which our project is stored on the remote host.    
- Then it uploads the contents of our directory  
- Converts CRLF to LF files  
- Fixes permissions so that you could use a user group for your project  
- Executes "./scripts/docker-build.sh".  
- Finally it UPs the `app` service.  
  
Something that you should note is that all your `.sh,Supfile.yml,Dockefile,docker-compose.yml` files **should be with LF line endings.**
If you're unsure you can always run `sup prod lf`, which should convert all files in a LF format.


To deploy the API, run `sup prod deploy`.
After this completes, you should be able to see the message "Hello world" when you open your <ssh host>:8089/ url

To see the output of your `app` service just run `sup prod logs`.  

<br>
<br>
<em style="font-size: 18px; color: rgba(0, 0, 0, 0.62);">
    <i>Netlyt is an AI-only research and development company. We tackle non-trivial problems using the latest technologies and our own solutions. We've built systems to <b>[prevent corruption in the police force, track and predict air pollution, reduce natural gas distribution costs, detect faults in the electricity grids in Africa](https://netlyt.io)</b> and others.</i>
</em>
