[comment]: # (THEME = sky)
[comment]: # (CODE_THEME = base16/zenburn)
[comment]: # (markdown: { smartypants: true })
[comment]: # (controls: false)
[comment]: # (keyboard: true)


### From Containers to Clusters
#### Exploring Docker & Beyond
----
Welcome to this Presentation!

[comment]: # (!!!)

### Table of Contents

- A Brief Overview
- Container VS Docker
- How You can Create a Container Image
- What is Docker Compose
- What are Container Orchestrations

[comment]: # (!!!)

## A Brief Overview

[comment]: # (!!!)

### Who Am I?

- **Mostafa Bayat**
- Bachelor's degree from the University of Tehran
- Four Years as a DevOps Engineer at Tapsell
    - More than One Year as A Technical Lead
- https://www.linkedin.com/in/mostafa-bayat/

[comment]: # (!!!)

The palest ink is better than the best memory!

Note: 
- This is a Chinese proverb
- Writing everything is very important
- Everything must be code and introduced through version control

[comment]: # (|||)

```sh [1-2]
git clone https://github.com/mostafabayat/docker-presentation
cd docker-presentation
```
Note: 
- Please pull and be active during presentation!

[comment]: # (!!!)

### Application Deployment History,
#### From baremetal to Serverless

![bare-vm-cont](media/concepts/bare-vm-cont.png)

Note:
- baremetal:
    - Performance impact by neighbours
    - security
    - hard management (e.g. high boot time, needs to know many things from hardware to application)
    - not scalable easily
- VM:
    - Hipervisor virtualizes hardware (drivers are critical!!!)
- Container:
    - Container Engine virtualizes the operating system (OS)
    - Containers share the same OS kernel
    - Very lightweight
- Serverless:
    - Serverless virtualizes the runtime environment.
- https://blog.equinix.com/blog/2020/02/25/how-to-speak-like-a-data-center-geek-bare-metal-vs-virtualization-vs-serverless

[comment]: # (|||)

Why do we need containers and container orchestration?

![container-meme](media/concepts/container-meme.jpg)

[comment]: # (|||)

### Who are Devops Engineer? SRE? Cloud Engineer? SysAdmin?

Note:
- Who are Devops engineers?
- All of these men are responsible for make an application accessible to the client.
- https://medium.com/@telesis/devops-sre-the-main-difference-678131b4081c

[comment]: # (|||)

### XaaS 

![xaas](media/concepts/xaas.jpg)
Note:
- https://www.ml4devs.com/articles/serverless-architecture-for-microservices-on-aws-vs-google-cloud-vs-azure-as-iaas-caas-paas-faas/

[comment]: # (!!!)

## Container VS Docker

![container-history](media/concepts/container-history.jpg)

Note:
- 2000: FreeBSD jails
- 2006: Process Containers by Google
- 2007: was renamed "Control Groups (cgroups)" eventually merged to Linux kernel 2.6.24
- 2008: LXC (LinuX Containers), implemented in 2008 using cgroups and Linux namespaces
- 2013: Docker, LMCTFY(by google, stopped in 2015 after Google started contributing core LMCTFY concepts to libcontainer), and Warden
- 2016-2017: Kubernetes
- 2020: Dockershim Removal from Kubernetes 
- https://www.aquasec.com/blog/a-brief-history-of-containers-from-1970s-chroot-to-docker-2016/
- https://www.redhat.com/en/topics/containers/whats-a-linux-container

[comment]: # (|||)

### What is a linux container?
------
Cgroup & Linux namespaces

Note:
- A Linux container is a set of 1 or more processes that are isolated from the rest of the system!
- The container has the necessary libraries, dependencies, and files
- cgroup: Linux kernel feature that could control resource usage for user processes.
- Linux namespaces: You can group of processes and share limitation between processes of a namespace
    - Network namespaces
    - Mount namespaces
    - PID namespaces
    - User namespaces
- https://medium.com/geekculture/introduction-to-containers-basics-of-containerization-bb60503df931

[comment]: # (!!!)

#### How You can Create a Container Image

Let's Write A Dockerfile

``` [1|2|3-4|5|6|7|8|9|10]
FROM python:3.10-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run", "--debug"]
```
Note:
- Use Entrypoint

[comment]: # (|||)

Let's Build
```sh [1|2]
docker build -t mostafabayat/docker-present:v1 examples/1-dockerize;
# docker push mostafabayat/docker-present:v1
```

[comment]: # (|||)

Let's Run
```sh [1]
docker run --name docker-present --rm --network host mostafabayat/docker-present:v1
```

[comment]: # (!!!)

#### What is Docker Compose

Run a Redis using docker and build&run 2nd example
```sh [1|2|3]
docker run -p 6379:6379 --rm --name redis --network host redis
docker build -t mostafabayat/docker-present:v2 examples/2-deploy-with-redis;
docker run --name docker-present --rm --network host mostafabayat/docker-present:v2
```

[comment]: # (|||)

Now write a docker compose

```
services:
  web:
    build: .
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
```