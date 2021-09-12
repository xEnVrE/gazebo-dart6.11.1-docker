# gazebo-dart6.11.1-docker

![badge](https://github.com/xenvre/gazebo-dart6.11.1-docker/workflows/Docker%20Image%20Build/badge.svg)

Dockerfile and image for Gazebo11 + DART 6.11.1 **with support for friction coefficients**.

Latest Ubuntu (including 21.10) provides `Gazebo 11.1.0` still lacking support for `mu1` and `mu2` surface friction coefficients  when using `DART` (it has been introduced in `Gazebo 11.4.0` with [this PR](https://github.com/osrf/gazebo/pull/2781) and requires compiling against `libdart >= 6.10`).

The latest [Dockerfile](https://github.com/osrf/docker_images/blob/1b0072a315610a6e009bcf09343d79255933cbb2/gazebo/11/ubuntu/focal/gzserver11/Dockerfile) from `OSRF` provides `Gazebo 11.8.1` but seems to be compiled with `libdart 6.9.2`.

This repository provides a Dockerfile (and associated image hosted in the GitHub `ghcr` registry) with Gazebo compiled against `libdart 6.11.1` within `Ubuntu 20.04`.

In order to use it please proceed as follows:

1. Pull the docker image:
    ```console
    docker pull ghcr.io/xenvre/gazebo-dart6.11.1-docker:latest
    ```
1. Launch the container:
    ```console
    docker run -it --user user --env="DISPLAY" --net=host --device /dev/dri ghcr.io/xenvre/gazebo-dart6.11.1-docker:latest
    ```
