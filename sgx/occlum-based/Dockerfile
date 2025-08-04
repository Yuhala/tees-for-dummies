FROM occlum/occlum:latest-ubuntu20.04

RUN apt-get update && apt-get install -y build-essential make vim libnuma-dev

# Set working directory 
WORKDIR /root/occlum-tests

COPY helloworld.c Makefile ./