# boot2docker

The following are instructions on how to get an IPython development environment up and running without having to install anything on your host machine, other than [`boot2docker`](https://github.com/boot2docker/boot2docker) and [`docker`](https://www.docker.com/).

## Install `boot2docker`

Install [boot2docker](https://github.com/boot2docker/boot2docker).  There are multiple ways to install, depending on your environment.  See the [boot2docker docs](https://github.com/boot2docker/boot2docker#installation).

### Mac OS X

On a Mac OS X host with [Homebrew](http://brew.sh) installed:

```
$ brew install boot2docker docker
```

### Initialize `boot2docker` VM


```
$ boot2docker init
```


### Start VM

```
$ boot2docker up
```

The `boot2docker` CLI communicates with the docker daemon on the `boot2docker` VM.  To do this, we must set some environment variables, e.g. `DOCKER_HOST`,

```
$ $(boot2docker shellinit)
```

To view the IP address of the VM:

```
$ boot2docker ip
192.168.59.103
```

## Install `ipython` from Development Branch

```
$ git clone --recursive https://github.com/ipython/ipython.git
```

## Build Docker Image

Use the Dockerfile in the cloned `ipython` directory to build a Docker image.

```
$ cd ipython
$ docker build --rm -t ipython .
```

## Run Docker Container

Run a container using the new image.   We mount the entire `ipython` source tree on the host into the container at `/srv/ipython`.  As a result, any changes we make to the source on the host are immediately reflected in the container.

```
# change to the root of the git clone
$ cd ipython
$ docker run -it --rm -p 8888:8888 --workdir /srv/ipython --name ipython-dev -v `pwd`:/srv/ipython ipython /bin/bash
```

To list the running container from another shell on the host:

```
$ $(boot2docker shellinit)
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
f6065f206519        ipython             "/bin/bash"         1 minutes ago       Up 1 minutes        0.0.0.0:8888->8888/tcp   ipython-dev    
```

## Run Notebook Server

```
$ ipython notebook --no-browser --ip=*
```

## Visit Notebook Server

On your host, run the following command to get the IP of the boot2docker VM if you forgot:

```
# on host
$ boot2docker ip
192.168.59.103
```

Then visit it in your browser:

```
# browser
http://192.168.59.103:8888
```

As a shortcut on a Mac, you can run the following in a terminal window (or make it a bash alias):

```
$ open http://$(boot2docker ip 2>/dev/null):8888
```