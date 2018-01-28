# Predictable application behavior with Docker

This portion of the workshop focuses on creating ML applications that produce preditable behavior, regardless of where they are deploy. We will utilize [Docker](https://www.docker.com/) to accomplish this goal. To take out as many workshop variables as possible and to optimize WiFi usage, we will be working with Docker on a cloud instance that we will utilize again later in the workshop for other things. However, you can do the exact same things below locally in a normal workflow.

The below sections walk you through the "Docker-izing" of an example ML application:

1. [Connect to your cloud instance](README.md#1-connect-to-your-cloud-instance)
2. [Clone the workshop materials](README.md#2-clone-the-workshop-materials)
3. [Docker-ize an example python ML application](README.md#3-docker-ize-an-example-python-application)
4. [Run a Docker container](README.md#4-run-a-docker-container)

Bonus:

5. [Push your Docker image to a registry](README.md#5-push-your-docker-image-to-a-registry)
6. [Build a minimal Docker image](README.md#6-build-a-minimal-docker-image)

Finally, I provide some [Resources](README.md#resources) for you for further exploration.

## 1. Connect to your cloud instance  

You should have been given an IP for a remote machine at the beginning of the workshop.  The remote machine already has Docker installed and all of the command line tools we will be needing throughout the workshop.  To log into the remote machine on Linux or Mac, open and terminal and:

```
$ ssh pachrat@<remote machine IP>
```

On Windows you can use PuTTY or another ssh client.  You will be asked for a password, which you should also be given during the workshop.  To verify that everything is running correctly on the machine, you should be able to run the following with the corresponding response:

```
$ docker --version
Docker version 1.11.2, build b9f10c9
```

## 2. Clone the workshop materials

You will need the workshop files and data on your machine to be able to go through all the examples. Fetch these files with:

```
$ git clone https://github.com/dwhitena/amld-reproducible-ml-workshop.git
```

## 3. Docker-ize an example Python application

Let's say that we want to "Docker-ize" [example/train.py](example/train.py). This means that we want to create a *Docker image*. The Docker image will include our application and any library/package dependencies. Once built, we can then run this Docker image as a *Docker container* on any machine that is running the *Docker engine*. Regardless of the host OS or local configuration, this will buy us predictable behavior and portability.

To Docker-ize `train.py` we need a *Dockerfile* that will tell Docker how to build the Docker image. Our example Dockerfile is included [here](example/Dockerfile). Once we have that Dockerfile, we can build the docker image using:

```
$ cd amld-reproducible-ml-workshop/docker/example
$ sudo docker build -t amld-test . 
``` 

This command will likely ask you for a sudo password. This will be the same password given earlier in the workshop to connect to the instance.

Once built, you should be able to see your image in the list of images on the machine:

```
$ sudo docker images
REPOSITORY                                               TAG                 IMAGE ID            CREATED             SIZE
amld-test                                                latest              d3be78f3bb51        18 seconds ago      1.236 GB
gcr.io/google_containers/kube-apiserver-amd64            v1.8.7              271d3dd5ab8e        11 days ago         194.7 MB
gcr.io/google_containers/kube-proxy-amd64                v1.8.7              125dec6bd8f2        11 days ago         93.37 MB
gcr.io/google_containers/kube-controller-manager-amd64   v1.8.7              d8df883aabf9        11 days ago         129.6 MB
gcr.io/google_containers/kube-scheduler-amd64            v1.8.7              f0325af1b839        11 days ago         55.13 MB
python                                                   latest              c1e459c00dc3        5 weeks ago         691.7 MB
pachyderm/dash                                           0.5.23              809ceec0c629        5 weeks ago         135.9 MB
pachyderm/pachd                                          1.6.6               2aeac001c88c        6 weeks ago         68.33 MB
weaveworks/weave-npc                                     2.1.3               eafec249fefe        7 weeks ago         46.55 MB
weaveworks/weave-kube                                    2.1.3               e102e3c2cf2e        7 weeks ago         92.59 MB
gcr.io/google_containers/k8s-dns-sidecar-amd64           1.14.5              fed89e8b4248        4 months ago        41.82 MB
gcr.io/google_containers/k8s-dns-kube-dns-amd64          1.14.5              512cd7425a73        4 months ago        49.39 MB
gcr.io/google_containers/k8s-dns-dnsmasq-nanny-amd64     1.14.5              459944ce8cc4        4 months ago        41.42 MB
pachyderm/grpc-proxy                                     0.4.2               c799876a153f        4 months ago        723.9 MB
pachyderm/etcd                                           v3.2.7              eb082d6ca0f2        4 months ago        47.08 MB
gcr.io/google_containers/etcd-amd64                      3.0.17              243830dae7dd        11 months ago       168.9 MB
gcr.io/google_containers/pause-amd64                     3.0                 99e59f495ffa        21 months ago       746.9 kB
```

## 4. Run a Docker container

After building the Docker image, we can run this Docker image on any machine that has Docker installed. Let's try just running it on this instance with:

```
$ sudo docker run -it amld-test /bin/bash
```

This will open up an interactive bash shell in the container. You can explore the container a bit and try running the code (`train.py`) that we added to the image.

## Bonus exercises

You may not get to all of these bonus exercises during the workshop time, but you can perform these and all of the above steps any time you like with a [local installation of Docker](https://www.docker.com/community-edition). 

### 5. Push your docker image to a registry

Generally, Docker images are stored and versioned in a *Docker registry*. [Docker Hub](https://hub.docker.com/) is an example of such a registry and is pretty convenient for development. If you have a Docker Hub account or if you create one, we can push our previously built image to the registry by (i) tagging the image with our Docker Hub username, and (ii) logging into Docker Hub, and (iii) pushing the image to Docker Hub.

You should be able to replace `dwhitena` below with your Docker Hub username:

```
$ sudo docker tag amld-test dwhitena/amld-test
$ sudo docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: dwhitena
Password:
Login Succeeded
$ sudo docker push dwhitena/amld-test
The push refers to a repository [docker.io/dwhitena/amld-test]
ec5f135f5bb2: Pushed
9ef903179678: Pushed
6dce5c484bde: Mounted from library/python
057c34df1f1a: Mounted from library/python
3d358bf2f209: Mounted from library/python
0870b36b7599: Mounted from library/python
8fe6d5dcea45: Mounted from library/python
06b8d020c11b: Mounted from library/python
b9914afd042f: Mounted from library/python
4bcdffd70da2: Mounted from library/python
latest: digest: sha256:37769825d1408cbb0a880bd574d025beb5d0e16ae37f4bbe2f18c4c58d8ac447 size: 2427
```

### 6. Build a minimal Docker image 

While the Docker image we built above is fine and is smaller than many Python deployments, we can do better. There are a variety of minimal linux distributions that we can leverage to build "minimal" Docker containers. In addition, by starting from one of these base images we avoid including any unnecessary Python components, which further minimize the size of the image. A smaller image is easier to upload/download and generally helps your applications spin up faster.

You can try using the [bonus/Dockerfile](bonus/Dockerfile) to build another Docker image for our application. Once build and/or pushed to the registry you will see that this image is significantly smaller than our original image.

## Resources

- [Getting started with Docker](https://docs.docker.com/get-started/)
- [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
