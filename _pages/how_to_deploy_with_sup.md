---
layout: post
title:  "How can I deploy with Sup?"
date:   2019-08-17 01:01:01 +0200
categories: DevOps
category: DevOps
description: Make your deployments easier without having to host as CI/CD. This post is the first in the deployment series.
cover: "https://pressly.github.io/sup/logo.svg"
author: Vasil Vasilev
---

Nowdays if you want to use CI/CD in your project, you can either use something like Jenkins, TravisCI, CircleCI, GoCD.
But what if you have a private project that's hosted on a google/amazon instance and you don't want to setup any of the previous tools?
**Sup** is a great tool that can help you out with that.
This guide will be covering the Windows setup. For mac and linux things are pretty similar.
We'll go over **installing git**, **setting up your wsl environment** and **installing go and sup** 

## Setting up GIT
First we'll setup our git.
If you already have git installed, feel free to skip to the [Configuration](#configuration) section.
### Installation
Download a fresh copy from [here](https://git-scm.com/download/win). 
Make sure you select:
- `Git from the command line and also from 3rd-party software`
- `Use the OpenSSL library`
- `Checkout as-is, commit as-is`  
The last option is really important, it will keep git from messing up the file line endings, which can cause issues.

### Configuration
It's recommended that you disable aurocrlf if you're not sure what you're doing.
Disable it by running:  `git config --global core.autocrlf true`

## Installing WSL(Bash on Ubuntu on windows)
[Here's](http://wsl-guide.org/en/latest/installation.html) a really easy guide on how you can setup WSL.

### Setting up SSH.
Start a **bash** shell.
If you don't have a ssh key, generate one by running `ssh_keygen -t rsa`, save it in the default directory and leave the password blank.
Now copy the contents of your **~/.ssh/id_rsa.pub** file into your clipboard.    
Here's an example:   
```bash
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDYcpjzQ4DDeq5JZwZ7y9Czbr6m55MAvhbmk63yzrvikE7ZkRxSck2iXPeTmrEhizViRN5cAB+I9X9n+cW9xTAcD2G/mRVlAyBkNKVvIXfTjSTrMBvp7ZazkXs0y7TdIegPOqzVE1KB6SzzZIKwHM/fy50GV3zrmv9FSG2aBu6Brt/ok/7mnrCzNhskZBi7Kt4De99nFFSecpFMyj4kwfcSSVCyL14pFWJQPw7u7vFe75qAQbO9bubwu9TKUckbXK5t5p/ETiLUTakblpqvh/qqU7xpmo1gmQ1fgrHUBYY0yhvi/i/vz9jFd7+8ly36csk8h+dmZqFjSYKdLyFNxMtT vasko
```
Go to the instances page on google cloud compute engine,
click *Edit* , scroll down to **SSH Keys**, click Show and edit, click on Add new item and enter your public (id_rsa.pub) key.  
With this you're registering your private/public keypair for the selected instance.  
Notice that your keypair ends with **username**. If it doesn't add the one that you desire to use.  
After that just press *Save*.  

Now open the ~/.ssh directory and create a file named **config** inside it.
You can do that by running 
```
cd ~/.ssh
nano config
```
In that file add something like that:
```
Host <ssh host>
    Hostname <ssh host>
    User <user>
    Port 22
    IdentityFile <path to id_rsa>
```
Make sure you replace the **user**, **ssh host** and **path to id_rsa** variables.  
For the *user* value use the username that you specified in your ssh key.  
Exit by hitting **ctrl + x**  
With this configuration you can easily access the ssh host without entering any password, assuming you did not use a password when you created your keypair.
Test it out by running `ssh <ssh host>`.  
You should be able to login without any password.

## Installing Golang
Run this
```bash
wget https://dl.google.com/go/go1.12.9.linux-amd64.tar.gz
tar -xvf go1.12.9.linux-amd64.tar.gz
sudo mv go /usr/local
```
### Setup environment
```bash
mkdir -p ~/go/bin
sudo nano ~/.bashrc
```
Scroll down to the bottom of the file and add this
```bash
# Go Global variables
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```
Press **ctrl + X** to exit.
Then just run `source ~/.bashrc` to update your environment.
To check if your go installation is ok, run `go version`, you should see something like this `go version go1.12.9 linux/amd64`


## Sup

Installing Sup is as easy as running `go get -u github.com/sp0x/sup/cmd/sup`.  It's the same command if you only want to update it.
To check if it's installed just run `sup -v`  
In my [next](/post) post I'll cover how to deploy a simple project with **Sup**.


<br>
<br>
<em style="font-size: 18px; color: rgba(0, 0, 0, 0.62);">
    <i>Netlyt is an AI-only research and development company. We tackle non-trivial problems using the latest technologies and our own solutions. We've built systems to <b>[prevent corruption in the police force, track and predict air pollution, reduce natural gas distribution costs, detect faults in the electricity grids in Africa](https://netlyt.io)</b> and others.</i>
</em>
