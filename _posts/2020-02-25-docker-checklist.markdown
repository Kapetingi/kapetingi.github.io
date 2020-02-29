---
layout: post
title:  "My checklist for build proper docker images"
date:   2020-02-25 22:21:53 +0100
categories: jekyll update
---
## This is my checklist to build proper docker image:

# Avoid a generic base image

Prefer images with the smallest presets. 
In the image should be present only those dependencies that are necessary to complete the task.
In this case, you not only minimize the size of the image, which is useful, 
but also reduce the number of vulnerabilities.

# Use user with reduced privileges scope

Always use `USER` command to change default user(which is root) to less privileged user, with roles
needed to run application. It reduced a scope of possible problem if application will be hacked. Try 
to reduce user shell and interactive mode as well

# Dot leak sensitive information into image

Instead of push sensitive information as secrets into image try to use 'docker secret', in case of k8s
use a mount volumes instead of put secrets as environment variables

# Use COPY instead of ADD

Docker provides two commands for copying files from the host to the Docker image when building it: COPY and ADD. 
The instructions are similar in nature, but differ in their functionality:

COPY — copies local files recursively, given explicit source and destination files or directories.
 With COPY, you must declare the locations.
ADD — copies local files recursively, implicitly creates the destination directory when it doesn’t exist,
 and accepts archives as local or remote URLs as its source, which it expands or downloads respectively into the destination directory.


# Use a tools for checking vulnerabilities

To automatically checking you docker image for knowing issues use tools like `hadolit`

# Sing your images

To avoid MITM attack sing images, in that case you can be sure that you run unmodified version of your application

# Useful commands 

This is a list of useful command that you may use in you daily works with docker

```shell script

docker -history <ImageId> # shows a all layers and it's size for paticular image

docker system df # shows a docker disk usage

```
