# AGL SDK Quick Setup
This tutorial explains how to quickly setup an environment suitable to building and packaging AGL Applications using the SDK and a Docker container.
The current tutorial has been tested on Linux, but may work with a few adjustments for Windows or MacOS.

## Step 1: Install Docker

First install docker on your host, if not already done.
General instructions for Linux are available on the [Docker Site](https://docs.docker.com/engine/installation/linux/).

## Step 2: install the "Generic AGL Worker" Docker Image
### Get docker image
#### Pre-built image

A pre-built image is available on [IoT.bzh](http://iot.bzh) public site and can be used directly.

First, download and load the image in your local Docker instance:

```bash
wget -O - http://iot.bzh/download/public/2016/docker/docker_agl_worker-2.1.tar.xz | docker load;
docker images;
      REPOSITORY                              TAG                 IMAGE ID            CREATED             SIZE
      docker.automotivelinux.org/agl/worker   2.1                 42009148bc03        6 days ago          926.9 MB
      jenkins                                 latest              55720d63e328        5 weeks ago         711.9 MB
      hello-world                             latest              c54a2cc56cbb        5 months ago        1.848 kB
```
#### Rebuilt image
The Docker image for AGL Worker can be rebuilt using the scripts published here [agl-docker-worker](https://github.com/iotbzh/agl-docker-worker).

### Start image
Then, use the 'create_container' script to start a new, fresh container based on the AGL Worker image:
**Note**:  
 * The password for the id 'devel' inside the docker image is 'devel'.

```bash
git clone https://github.com/iotbzh/agl-docker-worker;
cd agl-docker-worker;
./create_container 0;
docker ps;
	CONTAINER ID        IMAGE                                       COMMAND                  CREATED             STATUS              PORTS                                                                                        NAMES
	4fb7c550ad75        docker.automotivelinux.org/agl/worker:2.1   "/usr/bin/wait_for_ne"   33 hours ago        Up 33 hours         0.0.0.0:2222->22/tcp, 0.0.0.0:69->69/udp, 0.0.0.0:8000->8000/tcp, 0.0.0.0:10809->10809/tcp   agl-worker-odin-0-sdx
```

**Note**  
That the script 'create_container' will instantiate a new container and will share some volumes with the host:
* /xdt (the build directory inside the container) is stored in ~/ssd/xdt_$ID (specific to instance ID)
* /home/devel/mirror is stored in ~/ssd/localmirror_$ID (specific to instance ID)
* /home/devel/share => points to  ~/devel/docker/share (shared by all containers)



## Step 3: install the AGL SDK for your target

Here, we assume that we just built an image 'agl-demo-platform-crosssdk' using the Yocto build procedure documented in the [Getting Started](../) section of the documentation.

So we can copy such file to the shared volume.

For example, we could have built the SDK from another worker container listening with SSH on port 2223:

```bash
create_container 1;
ssh -p 2223 devel@mybuilder.local;
... [ prepare build environment ] ...
bitbake agl-demo-platform-crosssdk;
... [ build happens in /xdt/build ] ...
cp /xdt/build/tmp/deploy/sdk/poky-agl-glibc-x86_64-agl-demo-platform-crosssdk-cortexa15hf-neon-toolchain-3.0.0+snapshot.sh ~/share;
```

then login to the first "SDK Container" and install the SDK:

```bash
ssh -p 2222 devel@mysdk.local;
install_sdk ~/share/poky-agl-glibc-x86_64-agl-demo-platform-crosssdk-cortexa15hf-neon-toolchain-3.0.0+snapshot.sh;
```

## Step 4: build your application

You're ready to go: get the sources, run the builds ...

```bash
git clone <your repo for your app>;
cd <your app>;
cmake; make; make package;
```