## Exercise 2 for Cloud Computing ##

### Summer Semester 2024 ###

#### For more enquiries, please contact me ####

Dear students,

Welcome to the second exercise for Cloud Computing. After such hectic weeks, let's
dive a bit more into automation, because what could go wrong, right? 

During this assignment, you will learn how to install, use, and conquer the 
Docker Engine to virtualize all your applications! It makes a simple transition
whenever you want to try Github Actions (automating your code on repository changes)

The code base is the same as for the Exercise 1: feel free to change the `main.go`
and `index.html` to accomodate for your changes. However, you will need create a
few more files to pass this assignment. Let's see what this assignment needs!

> The code is available on [Github](https://github.com/CAPS-Cloud/cc-ss24-exercise-2)

### What is the task? ###

As you have seen in Piazza, during this assignment, you can continue submitting
your solution for Exercise 1 as you need it to pass both assignments. Additionally, 
you need to do:

1. Install the Docker Engine (see instructions below) on your machine and VM.
2. Create a **Dockerfile** that builds your application into an image.
3. Push your image into your Docker Registry Account (only public registries allowed!)
4. Use the Docker Engine to run MongoDB and your application on your VM.

The setup for MongoDB in your application has changed: instead of using a 
predefined URI (mongodb://...), you will have to **export** an *environment variable*, 
which couples with the automated tests and my automatic instance. Make sure to
provide the proper URI when running your applcation on your VM (hint: maybe you
use **Docker Compose**)

### Requirements and Test Scenarios ###

#### Requirements ####

1. You must create an **amd64** image (careful for those running ARM/Apple Silicon)
2. You must run your application and MongoDB using Docker 
3. The URI for MongoDB must be given via an **environment variable** 
(it is already in the code, you must define it before running your application)
4. The exposed port describe in your Dockerfile must match with your application
port.
5. The automated tests from Exercise 1 must pass.

#### Tests ####

1. Using an isolated MongoDB instance, the server will download, configure, and
run your image (10 pts).
2. Using my local execution of your application, I will perform the same tests as
Exercise 1 (45 pts).
3. Using the endpoint, I will perform the same tests as those locally (45 pts).

### What do you need for this assignment? ###

The starting point is the code from this file and your solution from the Exercise 1.
Additionally, you need to **install** the Docker Engine.

#### Installing Docker ####
> **WARNING FOR WINDOWS/MAOS USERS**

> You cannot install the Command Line Interface (CLI), you have to use **Docker
> Desktop**, that install all you need. For Windows users, please look at tutorials
> on how to build images as I am not familiar with it. For MacOS users, after
> installing **Docker Desktop**, you can use the CLI commands to build your 
> images

To install the **Docker Engine**, please follow [this tutorial](https://docs.docker.com/engine/install/)
depending in your Operating System. The Docker Desktop is a wrapper for the Docker Engine. For your VM,
you must follow the Linux instructions!

> **WARNING FOR LINUX USERS**

> After the installation, you will have to use `sudo` before running any command,
> to avoid such annoyances, you can execute the following:
```bash
$ sudo groupadd docker # it could fail if the group already exits, ignore it
$ sudo usermod -aG docker $USER
$ sudo newgrp # it reloads the groups at runtime without logging out
```

(Optional) You do not need Docker Compose to run your containers, but it can
automate building and launching contianers at will. To do so, please install it
using [this tutorial](https://docs.docker.com/compose/install/)

#### Using Docker ####

Once you have installed Docker, you can use it from the CLI (Terminal) as follows:

```bash
$ sudo docker build -t <registry_url>/<username>/<image_name>:<tag> . # builds the image using the local (.) Dockerfile
$ sudo docker image ls # Lists all local images
$ sudo docker ps # Lists all running containers
$ sudo docker stop <container_id> # Stops a running container, the id is shown when executing the command above
$ sudo docker rm <container_id> # Removes an **stopped** container
$ sudo docker rmi <image_id> # Remove an image without containers related to it
$ sudo docker run # append the necessary options below, only --rm is optional
                  --rm # Removes container when it exits
                  -it # Connect to the container's terminal
                  --name <container_id> # Gives the container a name
                  -p "5000:6000" # Maps the internal exposed port (6000) to an external port (5000)
                  -v <host_path>:<container_path> # mounts a volume into the container (like mounting a disk or usb stick)
                  <image> # the name of image, the one from the first command
                  /bin/sh # the command to run once it connects to the terminal, it depends on the virtualized OS of the container
```
> PS: For those with a different CPU architecture than x86_64 (ARM/Apple Silicon),
> you will have to define the target platform your image should have. To do so,
> you can append an option to the build command as `--platform=linux/amd64`. 
> If you want to also run your image locally, you need to build a **multi-platform**
> image by specifying multiple platforms separated with commas as:
> `--platform=linux/amd64,linux/arm64`. You also need to pass the architecture
> to the `FROM` command by doing `FROM --platform=$TARGETPLATFORM <base_image>`

#### Exporting environment variables ####

Since this assignment requires configurable parameters (the database's URI),
you will have to *create variables* in your current terminal, if you run your
application without **Docker** or **Docker Compose**. To do so, you will run:

```bash
$ export DATABASE_URI=mongodb://.... # Linux and MacOS
setx DATABASE_URI "mongodb://..." # Windows
```

In case you are using docker containers, you need to pass those environment
variables to the container upon running it. For this purpose, you need to append
an argument to the `run` command by doing `-e DATABASE_URI="mongodb://...`.
The cool feature is that you append this argument multiple times to define multiple
environment variables. Great, right?

####  [Optional] Using Docker Compose ####

Since most cases consist of local development, the overhead of copying for code
between your machine and the VM might be high. Hence, we can automate the 
deployment of containers locally, especially to ease how connected services
interact. For this purpose, we can use **Docker Compose**: you will
have to create a `docker_compose.yml` file, similar to the one in the exercise
slides.

Once the file is created, you can use the following commands to launch, stop, 
pause, and interact with the running containers:

```bash
$ sudo docker compose build # builds/rebuilds all images according to the file
$ sudo docker compose build <image_name> # builds/rebuilds only one of the services
$ sudo docker compose build --push # pushes the image after building
$ sudo docker compose push # pushes the build images according to the file
$ sudo docker compose up # (interactive) launches containers according to the file
$ sudo docker compose up -d # (detached) launches containers according to the file in the background
$ sudo docker compose stop # stops all containers according to the file 
$ sudo docker compose kill # kills all containers according to the file
$ sudo docker compose rm # removes all containers according to the file (be aware of created data at runtime and the thin R/W layer)
```
> Remember to define the environment variables in your **Docker Compose** for each
> service defined in the file.

> For more on how to use **Docker Compose**, you can run `sudo docker compose --help`
> or `sudo docker compose [subcommand [subsubcommand]] --help`.

> PS: when a command takes optional arguments, they are usually wrapped in square
> brackets, as previously for `[subcommand]`. It can be a nested structure as before.
> If the arguments can be a list and optional, it will be `[subcommand ...]`

Without further ado,

#### Happy Coding! ####
