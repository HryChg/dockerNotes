# Docker
#docker
These information are learned from [The Docker Handbook – 2021 Edition](https://www.freecodecamp.org/news/the-docker-handbook/)

# Vocab
## Containerization
**Containerization** involves **encapsulating** or packaging up software **code** **and** all its **dependencies** so that it can **run** **uniformly** and consistently on **any infrastructure**.

In other words, it bundles up the dependencies in a container so that it can be run without going through a troublesome set up process.

## Container
An isolated environment that lets you develop and run the application from within. It should match your final deployment environment.

Consider containers like the next generation of virtual machines.  They are isolated from the host system as well as from each other yet they are lighter than traditional virtual machines so you can run multiple containers at once.

## Image
A single file that contains the software code along with all its dependencies and necessary configurations.

Images are multi-layered self-contained files that act as the template for creating containers. They are like a frozen, read-only copy of a container.  Images can be exchanged through series.

Containers are just images in running state.
You would obtain an image from the internet and run a container using that image. Basically, you are creating another temporary writable later on top of the previous read-only ones.

## Registry
A central server used to share a new Image to anyone with proper authorization.

An image registry is a centralized place where you can upload your images and download images created by others. **Docker Hub** is the default public registry for Docker.

## Docker
Docker is an open-sourced implementation on Containerization that allows you to containerize our application, share them using public or private registries, and also to orchestrate them.

Docker Desktop Package is a collection of tools like
- `Docker Engine`
- `Docker Compose`
- `Docker Dashboard`
- `Kubernetes`

## Docker Daemon
The daemon (called `dockered`) is a process that keep running in the background and waits for command from the client. The daemon is capable  of managing various Docker objects.

## Docker Client
The client (called `docker`) is a command-line interface (CLI) program mostly responsibility for transporting commands issued by users

## Docker is a client-server architecture
Docker uses a client-server architecture. The Docker **client** works by using the REST API to reach out to the background Docker **daemon** and get the work done.

## Bind Mounts
Bind mounts lets you form a two way data binding between the content of a local file system directory (source) and another directory inside a container (destination).  This way, any changes made in the destination direction will take effect o the source direction and vice versa.


# Docker Commands
### Check if docker contains is running
`docker info`

### List out all docker container IDs
- show only running containers:  `docker container ls`
- show all containers (incl. stopped ones): `docker container ls -a`
>>>
```shell
CONTAINER ID   IMAGE             COMMAND                  CREATED       STATUS      PORTS                                       NAMES
18bedf7fbdf8   html-to-pdf       "docker-entrypoint.s…"   13 days ago   Up 4 days   0.0.0.0:9423->3000/tcp, :::9423->3000/tcp   allthethings_html-to-pdf_1
369c24bf62d4   fhirbase:latest   "docker-entrypoint.s…"   13 days ago   Up 4 days   0.0.0.0:5433->5432/tcp, :::5433->5432/tcp   allthethings_fhirbase_1
```

### Kill a docker container
`docker container kill CONTAINER_ID`
Or multiple of them
`docker container kill CONTAINER_ID1 CONTAINER_ID2 CONTAINER_ID3`

### How to Restart a Container
Know that stopped containers remain in your system. If you want you can restart them.

 `docker container start CONTAINER_ID`
Or 
`docker container restaret CONTAINER_ID`

The main difference between two command is that `container restart` will first try to stop the target container if it is running.

### How to remove container
`docker container rm CONTAINER_ID`
Or multiple of them
`docker container rm CONTAINER_ID1 CONTAINER_ID2 CONTAINER_ID3`

### How to remove a container AUTOMATICALLY after it stops
There is also the `--rm` option for the container run  and container start commands which indicates that you want the containers removed as soon as they’re stopped
```shell
docker container run \
	--rm \
	--detach \
	--publish 8888:80 \
	--name hello-dock-volatile fhsinchy/hello-dock
```



==💩TODO== https://github.com/thrivehealth/allthethings/pull/7322/files
Figure out how to rm docker container in `docker-compose.yml` for api-pilot debugging in web storm

### Remove Dockers Using Regex on Docker Image Name
```shell
docker container ls -a | awk '{ print $1,$2 }' | grep alpine:3 | awk '{print $1}' | xargs -I {} docker rm {}
```

- `docker ls -a | awk '{ print $1,$2 }'` prints out the container IDs and image names
- Using the results from above,  `grep alpine:3` filters out containers with image name `alpine:3`
- `awk '{print $1}'` extracts only the containers IDs from the results above
- `xargs -I {} docker rm {}` lets you pass in all the containers with image named `alpine:3` and delete them all


### How to Name or Rename a Container
Naming a container can be achieved using the —name option.
To run another container using the `fhsinchy/hello-docker` image with the name `help-dock-container` you can execute the following command:
```shell
docker container run \
	--detach \
	--publish 8888:80 \
	--name hello-dock-container fhsinchy/hello-dock
```

- `detach` means detach the container with the terminal window so it can run in the background
- `name` lets you rename `fhsinchy/hello-dock` to `hello-dock-container`

You can even rename an existing container
```shell
docker container rename <CONTAINER_ID> <NEW_NAME>
```

## Running Firefox in Docker Container
Follows this tutorial: [GitHub - jlesage/docker-firefox: Docker container for Firefox](https://github.com/jlesage/docker-firefox#docker-compose-file)

1. Run This
```shell
docker run -d \
    --name=firefox \
    -p 5800:5800 \
    -v /docker/appdata/firefox:/config:rw \
    --shm-size 2g \
    jlesage/firefox
```

If you run into errors such as `Mounts Denied`, go to `Docker → Preference → Resources → File Sharing` and add the directory that is denied. In this case, the directory is `/docker/appdata`. (Note you can directly input the path and hit `enter`. Then you can run `docker run Firefox` in the terminal to continue.

2. Navigate to http://<HOST-IP-ADDRESS>:5800/ (In my case : http://192.168.86.72:5800/ )

### Note:

- You could even use HTTPS to connect the the Firefox in the container (checkout Security in README of the GitHub repo)
- If you would like to route Docker Traffic Through VPN → [Routing Docker traffic through a VPN connection | Jordan Elver | Ruby on Rails Developer, Bristol, UK](https://jordanelver.co.uk/blog/2019/06/03/routing-docker-traffic-through-a-vpn-connection/)
- There are also ways to run any GUI app in a docker container → [GitHub - jlesage/docker-baseimage-gui: A minimal docker baseimage to ease creation of X graphical application containers](https://github.com/jlesage/docker-baseimage-gui)


# How to Work with Executable Images
The problem is that containers are isolated from the local system, so your executable program running inside the container does not have access to your local file system. 

Fortunately you can map the local directory to the `/zone` folder inside the container. With that, the files should be accessible to the container.

## Bind Mounts
Bind mounts lets you form a two way data binding between the content of a local file system directory (source) and another directory inside a container (destination).  This way, any changes made in the destination direction will take effect o the source direction and vice versa.

Lets see a bind mount in action, suppose we have this docker container that removes files by the extension name
```shell
cd ~/Developer/dockerplayground
docker container run --rm -v $(pwd):/zone:rw fhsinchy/rmbyext txt
```

- `--rm`: remove the container after execution
- `-v $(pwd):/zone:rw` 
	- `-v` or `-volume`: is used to create a bind mount for a container. This option takes three fields separated by colons (`:`) as follow:
		- local file system absolute path (e.g. `$(pwd)` refers to current directory)
		- container file system absolute path (e.g. `/zone` refers to the root directory in the container `fhsinchy/rmbyext`)
		- read write access (optional)
			- this is optional param. You can skip it.
			- `rw` grants access to both read and write
			- `ro` read only

# How to containerize a JavaScript Application
## How to write the Development Dockerfile
	1. Create a `Dockerfile.dev` file in the **src** directory of your node project, which in our case is `hello-docker` project
	2. Within the `Dockerfile.dev` file, enter the following:

```Dockerfile
FROM node:lts-alpine

EXPOSE 3000

USER node

RUN mkdir -p /home/node/app

WORKDIR /home/node/app

COPY ./package.json .

RUN npm install

COPY . .

CMD ["npm", "run", "dev"]
```

		- `FROM node:lts-alpine`: Since we are building n image that runs on node, we must first use the official Node.js image as the base image and layer our own image on top of it.  The `FROM`  gets the base image while `its-alpine` tag indicates you want the use the **Alpine variant, Long Term Support version** of the node image.
		- `EXPOSE 3000`: The default port in our project folder runs on port 3000, therefore we can add this `EXPOSE` command to make this explicit.
		- `USER node`: Set the default user of `node:lts-alpine` to `node`
			- By default Docker runs containers as the **root user**
			- According to  [Docker and Node.js Best Practices](https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md) , this is a security thread. So it is better to run the image as a **non-root user** whenever possible.
			- In this case, the `node:lts-alpine` base image comes with a non-root user named `node` 
			- `USER node` let you set `node` user as the default user of the `node:lts-alpine`  base image.
		- `RUN mkdir -p /home/node/app` : create a directory called `app` insides the home directory of the `node` user
			- `/home` is the root directory of the `node:lts-alpine`  base image, it is also the root directory of the root user
			- `/home/node` is the root directory of the none-root user `node` of  the `node:lts-alpine`  base image. In general, the home directory for any non-root user in Linux is  `/home/<user_name>` by default
		- `WORKDIR /home/node/app`: sets the default working directory to the newly created `/home/node/app` directory.
			- By default, the working directory of any image is the root.
			- By setting a specific working directory, we can avoid any unnecessary files sprayed all over the root directly
			- ~Any subsequent COPY, ADD, RUN or CMD instructions from docker will be performed in the newly defined working directory~
		- `COPY ./package.json .`: Copies the `package.json` file to the root of the working directory (defined previously as `/home/node/app`. 
		- `RUN npm install`: runs `npm install` in the working directory
		- `COPY . .` the rest of the contents from the current directory (`.`) from the host system to the working directory (`.` or `/home/node/app`) inside the image
		- `CMD ["npm", "run", "dev"]`: sets the default command for this image (`npm run dev`) when running `exec` on this new image

3. Now to build the image from this new `Dockerfile.dev`, we can execute execute  command below at the directory of `Dockerfile.dev`
```shell
docker image build \
	--file Dockerfile.dev \
	--tag hello-dock:dev \
	.
```
	- `--file Dockerfile.dev`: Given the filename is not Dockerfile you have to explicitly pass the filename using the `—file`  option
	- `--tag hello-dock:dev`: We are calling this new docker image `hello-dock:dev`
	- Note the last line contains `.` which specifies the directory containing the dockerfile
	- This `hello-dock:dev` image will then be saved to Docker itself, not stored in the local directory.

4. Now to create a container base on this new image, we can do
```shell
docker container run \
    --rm \
    --detach \
    --publish 3000:3000 \
    --name hello-dock-dev \
    hello-dock:dev
```
	- `--name hello-dock-dev`: names the new container `hello-dock-dev`
	- `hello-dock:dev` is the image we are using

5. Now visit localhost:3000 to see the hello-dock application in action.


## How to Work with Bind Mounts in Docker

==💩TODO== 


## How to compose projects using Docker-Compose

Compose is a tool for **defining** and **running** **multi-container Docker applications**. With Compose, you use **YAML file** to configure your application’s services. Then with a single command, you create and start all the services from your configuration.

Although Compose works in all environments, it is more focused on development and testing. Using **compose** on a **production environment** is **not recommended** at all.

### Docker Compose Basics
```Dockerfile
# stage one
FROM node:lts-alpine as builder

# install dependencies for node-gyp
RUN apk add --no-cache python make g++

WORKDIR /app

COPY ./package.json .
RUN npm install

# stage two
FROM node:lts-alpine

ENV NODE_ENV=development

USER node
RUN mkdir -p /home/node/app
WORKDIR /home/node/app

COPY . .
COPY --from=builder /app/node_modules /home/node/app/node_modules

CMD [ "./node_modules/.bin/nodemon", "--config", "nodemon.json", "bin/www" ]
```

- `node-gyp`: node-gyp is a cross-platform command-line tool written in Node.js for **compiling native addon modules** for Node.js. And according to the  [installation instruction](https://github.com/nodejs/node-gyp#installation)  in the  [official repository](https://github.com/nodejs/node-gyp) , this build tool requires Python 2 or 3 and a proper C/C++ compiler tool-chain.


Now in the `node-api` project, there are two containers:
- `nodes-db`: A PostgreSQL database server
- `nodes-api`: A REST API powered by Express.js


In the world of Compose, **each container** that makes up the application **is** known as **a service**. We need to defined these service when composing a multi-container project.

Just like the Docker daemon uses a `Dockerfile` for building images, Docker Compose uses a `docker-compose.yaml` file to read service definitions from.

Head to the `notes-api` direction and create a new `docker-compose.yaml` with the following code:
```yaml
version: "3.8"

services: 
    db:
        image: postgres:12
        container_name: notes-db-dev
        volumes: 
            - notes-db-dev-data:/var/lib/postgresql/data
        environment:
            POSTGRES_DB: notesdb
            POSTGRES_PASSWORD: secret
    api:
        build:
            context: ./api
            dockerfile: Dockerfile.dev
        image: notes-api:dev
        container_name: notes-api-dev
        environment: 
            DB_HOST: db ## same as the database service name
            DB_DATABASE: notesdb
            DB_PASSWORD: secret
        volumes: 
            - /home/node/app/node_modules
            - ./api:/home/node/app
        ports: 
            - 3000:3000

volumes:
    notes-db-dev-data:
        name: notes-db-dev-data

```


- HighLevel
	-  `version: "3.8"`: Every valid `docker-compose.yaml` file starts with defining the file version. You can look up the latest version  [here](https://docs.docker.com/compose/compose-file/) .
	- `service`: The `service` block holds the definition of each of the services (i.e. containers) in the applications (which are `db` and `api`)
		- `db`:  The `db` block defines a new service in the application and holds all necessary information to start the container. 
			- Every Service requires either a pre-build image or a Dockerfile to run a container.
			- For the `db` service we are using the official PostgreSQL image
		- `api`: A pre-build image for the `api` service doesn’t exists. So we will use the `Dockerfile.dev` file created earlier
	- `volumes`: The `volumes` block define any name needed by any of the services.
		- Here we are only enlisting `notes-db-dev-data` volume used by the db service.
		- Any named column in any of the service has to be define there. If you don’t define a name from the volume in service (i.e. `notes-db-dev-data`),  the volume will be named following the `<PROJECT DIRECTORY NAME>_<VOLUME KEY>` .


After the high level over view, let’s take a closer look at the individual services.

- db
	- `image`: hold the image repo and tag used for this container. We are using postgres:12 image for running this db container
	- `container_name`: By default the containers are amend following <PROJECT_DIRECTORY_NAME>_<SERVICE_NAME> syntax. Here we can override it using `container_name` attribute
	- `volumes`: The `volumes` array hold the volume mappings for the service. It supports named volumes, anonymous volumes, and bind mounts.
		- You can see the syntax `<SOURCE>:<DESTINATION>` is identical to what we’ve seen before
	- `environment` : The `environment` holds them values of various environment variables needed for the service

- api
	- `build`: The `api` service does not come with a pre-built image. Instead we have a build configuration. Under the `build` block, we define the context and the name of the Dockerfile for building an image.
		- The `context` means the directory accessible by the daemon during the build process.
	- `image`: The `image` key holds the name of the image being built
	- `environment`: Inside the `environment` map
		- `DB_HOST` variable demonstrates a feature of Docker Compose
			- That is, you can ~refer to another service in the same application by using its name~. So the `db` here will be replaced by the IP address of the `api` address of the db service container
			- The `DB__DATABSE` and `DB_PASSWORD` have to match up with the `POSTGRES_DB` and `POSTGRES_PASSWORD` respectively from the db service definition

### How to Start Services in Docker Compose

One command in Docker Compose we must know is `up` command. The `up` command **builds** any **missing images**, **creates containers**, and **start them** in one go.

Before running docker-compose, make sure to navigate to the directory where `docker-compose.yaml` file is.

Lets navigate to `notes-api` folder and execute 
```shell
docker-compose --file docker-compose.yaml up --detach
# or
docker-compose up --detach
```

- `--file docker-compose.yaml`:  The `—file` or `--f` option is only needed if the YAML file is not named docker-compose.yaml. In our case, we added the `--file` flag only for demonstrating purposess
- `--detach or -d`: detaches the process from the terminal window

### You can also start individual services in the docker-compose files:
```shell
docker-compose up --detach db # start up only db container
docker-compose up --detach api # start up only api container
```






