---
layout: post
title:  "My checklist for build proper docker images"
date:   2020-02-25 22:21:53 +0100
categories: jekyll update
---
# This is my checklist to build proper docker image:

## Size

Prefer to choose a image with smaller size to bigger one. Then smaller base image you pick up 
then faster will be your builds and deployments. Try to reduce all not needed dependencies.


## Single responsability

Reduce all unnecessary dependencies. Close all ports unnecessary for the task.

## Security

Think about security. Never run application inside container as a root user. Create user, reduce his rights to 
necessary set of roles to fullfill task. Run application with this user. 


