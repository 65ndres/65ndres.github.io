---
title: Deploy your Docker Rails app to AWS
date: 2024-05-02 12:00:00 -500
categories: [programming]
tags: [tutorial,docker,rails,aws,nginx]
---

In a previous post we went over the process to move your Rails back end and React front end to docker locally, in this post we will talk about moving the same set up to a production environment.

## Game Plan

Launch EC2 intance from aws
    Choose Ubuntu image
    Choose Image size (use t2.micro if you want it for free)
    Create new key pair
        choose RSA and save it as a pem file (we might need)
    Create new security group 
    allow ssh, http and https conection from the internet

Use git to get our code to our new aws server
    Create git repo in Github
    push code to github
    


