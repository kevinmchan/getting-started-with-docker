# Getting started with Docker for machine learning

Sometimes setting up your machine learning development environment can be a hassle. This can be particularly true for packages with complicated dependencies, for example, a gpu enabled installation of tensorflow - with precise CUDA dependencies that change with package updates.

Once you've set up your development environment, there's also the issue of deploying your model in production, which can come with additional challenges of ensuring that your environment is consistent on a different machine.

We can attempt to solve both of these issues with docker containers. Often, the environment we need is already available as Docker images and we can be confident that the Docker images we use in development will be the same in production.

## Installation

Docker installation instructions: https://docs.docker.com/engine/install/

Nvidia docker installation instructions, for dGPU support: https://github.com/NVIDIA/nvidia-docker

## Using an existing docker image

### Running a tensorflow container

Let's run a gpu enabled tensorflow notebook. 

```bash
docker run -it --rm -v $(realpath ./notebooks):/tf/notebooks -p 8888:8888 tensorflow/tensorflow:latest-gpu-jupyter
```

### Running an anaconda container

## Creating our own docker image

```docker
FROM tensorflow/tensorflow:latest-gpu-py3
COPY tensorflow_example.py .
CMD ["python", "tensorflow_example.py"]
```

```bash
docker build --tag example_tf_model .
docker run --gpus=all example_tf_model
```

## Registering our image

## Docker CLI Cheatsheet

### Run
`docker run [-i] [-t] [--publish <host port>:<container port>] [--detach] [--name <alias>] <image name>`

`-i`: run interactively

`-t`: attach to terminal session

`--detach`: run in the background

`--publish <host port>:<container port>`: mapping of ports between host and container

`--name <alias>`: create an alias that can be used to refer to the container in the future 

### Stop

`docker stop <container name>`

### Remove

`docker remove [--force] <container name>`

`--force`: stop a running container so it can be removed 

### Build

`docker build [--tag <tag>] [--target <stage>] [-f <path to a Dockerfile>] <path to build context>`

`-t, --tag <tag>`: name and tag in the format `[repository/]name[:tag]` for refering to the image

`--target <stage>`: stop docker build at specified stage in multistage builds

`-f <path to a Dockerfile`: specify a custom location of the dockerfile; otherwise look for `Dockerfile` in in root of build context 

### Tag

`docker tag <tag>`

### Pull

`docker pull`


### Create
`docker container create`


### Push
`docker push`

## Multistage build

```docker
FROM golang:1.7.3 AS builder
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /go/src/github.com/alexellis/href-counter/app .
CMD ["./app"]  
```

```docker
FROM alpine:latest as builder
RUN apk --no-cache add build-base

FROM builder as build1
COPY source1.cpp source.cpp
RUN g++ -o /binary source.cpp

FROM builder as build2
COPY source2.cpp source.cpp
RUN g++ -o /binary source.cpp
```

## CI/CD with Docker Hub

...