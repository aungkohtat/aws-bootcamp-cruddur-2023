# Week01 — App Containerization

## Technical Tasks 

-	Create a new GitHub repo
-	Launch the repo within a Gitpod workspace
-	Configure Gitpod.yml configuration
-	Clone the frontend and backend repo
-	Explore the codebases
-	Ensure we can get the apps running locally
-	Write a Dockerfile for each app
-	Ensure we get the apps running via individual container
-	Create a docker-compose file
-	Ensure we can orchestrate multiple containers to run side by side
-	Mount directories so we can make changes while we code

## VSCode Docker Extension
[VSCode with Containers Overview](https://code.visualstudio.com/docs/containers/overview)
&NewLine;
&NewLine;
&nbsp;

### Docker 
- A ‘Platform as a Service’ that allows for the build, test, and deployment of applications without impacting the rest of your system.  
- Creates `Containers` that are isolated, virtual machine-like  environments that hold everything needed to run an application, including the operating system dependencies and source code. The Container shares the host operating system kernel. It is considered lightweight and good for one time use because it’s only running processes and not a whole operating system. 
- Since a Container is only running processes it can launch instantaneously, which is a much quicker deployment of a software package than if a VM with a full operating system were run.
- Instructions to create a Container are stored in a file called `Dockerfile`.
- Docker uses a CLI to send commands to a docker daemon, which is responsible for managing containers and their images.
  
&NewLine;
&NewLine;
&nbsp;
NOTE:
- Usually Gitpod is pre-installed with the Docker extension.
- I did not have the Docker extension, so I added it to my Gitpod VSCode.

### Dockerfile
-	Dockerfile is a text file that holds all the commands needed to call on the command line to build a Container based on a base image.  The commands are run in order listed in the file.
-	
-	Base images, or Container images, are stored in a registry.
-	`Docker Hub` is the most popular public registry and community for Container for base images.
   
[Docker Hub website](https://hub.docker.com)
  

## Containerize Backend
Create a server-side Docker container from a Docker image.
&NewLine;
&NewLine;
&nbsp;

Note:  `WORKDIR` container command in Dockerfile is an instruction that makes the working directory for any commands in the Dockerfile.  


### Add Dockerfile
- Bootcamp template already provided a server-side directory called /backend-flask
- Create new file within the backend-flask directory called `Dockerfile` and paste in the following base image code:

```sh
FROM python:3.10-slim-buster
WORKDIR /backend-flask
COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt
COPY . .
ENV FLASK_ENV=development
EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

![Backend-flask Container with code](/_docs/assets/dockerfile.jpg)

- Used command `ls` to list files/directories to see backend-flask  directory on local host.
- Used command `cd` backend-flask/ to change into the backend-flask directory
- Used command `ls` to see that the file called Dockerfile is listed in my backend-flask directory

![Show backend-flask dir and Dockerfile](/_docs/assets/docker_dir.jpg)


&NewLine;
&NewLine;
&nbsp;


### Install `python:3.10-slim-buster` 
- Listed next to FROM in `Dockerfile`
-  `python:3.10-slim-buster`  contains the python libraries to install to run the Cruddur app. Install in `backend-flask' directory:

- RUN pip3 install -r requirements.txt in the `backend-flask` directory
```sh
pip3 install -r requirements.txt
```

Proof of successful Python install:
&NewLine;
&NewLine;
&nbsp;

![successful python install](/_docs/assets/requirement.jpg)

Install the "flask" Python module, binding it to the i.p address "0.0.0.0" on port "4567" 
_By default flask would run on 127.0.0.1 localhost, but while running containers, we need to bind it to 0.0.0.0

`python3 -m flask run --host=0.0.0.0 --port=4567`


![Flask running](/_docs/assets/python.jpg)

- Check the "ports" tab and click the "lock" button to "unlock" it and make it publically assessible.
- Click the "address" to open it in a new tab
![Flask running on port 4567](/_docs/assets/notfound.jpg)

- So far we have been able to verify that our Flask server is running and accepting request

- It is however giving 404 error for the resource

- We get a "404" error - File not found
![404 error](assets/week1_flask_app_not_found.png)
- So far we have been able to verify that our Flask server is running and accepting request
- It is however giving 404 error for the resource
![404 error in terminal](/_docs/assets/404.jpg)

**So we now troubleshoot!!**
- we just remebered that we forgot the set some environment variables, required for the app to work.
- Let's set these env vars and re-run our app!
`export FRONTEND_URL="*"`

`export BACKEND_URL="*"`


`export BACKEND_URL="*"`

- Try again and this time append to the app url "api/activities/home"
![Flask works](/_docs/assets/api1.jpg)

![Flask works](/_docs/assets/api2.jpg)


- I got back JSON which is the output I should see.
&NewLine;
&NewLine;
&nbsp;

Note: There is a difference between RUN and CMD. RUN is used to create a layer in an image file.  CMD essentially is the command the container is going to run when it starts up. RUN as set of processes need to put in the container image and build the image.
&NewLine;
&NewLine;
&nbsp;
- To stop the server press `CTRL + C` to quit, which sends a kill command to the server.
- Two other ways to stop server is to click on the trashcan icon at top right of terminal screen.  Or click on 3 bars in upper left of Gitpod and click on terminal and run new terminal.



- Unset the backend and frontend environment variables in case they cause issue when I run a new container file.
```sh
unset FRONTEND
unset BACKEND
```

- Check that the backend and frontend variables are gone.
```sh
env | grep _URL
```

### Build Container Image
`docker build -t  backend-flask ./backend-flask`
- -t is for tagging a name.
- docker looked for the "Dockerfile" we created earlier in the "backend-flask" folder and used it to build the docker image 
- image was built in "./backend-flask", i.e. our "work directory"

![container image built](/_docs/assets/dockerimg.jpg)


We can see this image by click on the "Docker" extension.
Alternately, we can run the command:

` docker images	`

**Docker help**
` docker build --help `


### Run Container
` docker run --rm -p 4567:4567 -it backend-flask `

Let's check the app url to see if it works
_Note: we are expecting it to fail since we have not created the environment variables_

- I got a 404-ERROR as expected because I still need to reset the environment variables.

- Stop the container either by `CTRL + C`.  OR click on the container file under CONTAINERS in the docker extension, right click on STOP.
- Set the frontend and backend environment variables locally.
```sh
export FRONTEND_URL="*"
```
```sh
export BACKEND_URL="*"
```

- Run the docker container image with the environment variables.
```sh
docker run --rm -p 4567:4567 -it -e FRONTEND_URL=’*’ -e BACKEND_URL=’*’ backend-flask
```

- I got a 404-ERROR after running the container.


- Then, I added  `/api/activities/home ` to the end of the URL in order to access data on server.
- Go into new Bash terminal  and run command `docker ps` to see running containers.
```sh
docker ps
```
- Need to kill container to remove it. Above, I used `--rm` at in the RUN command to kill container right after is run.


&NewLine;
&NewLine;
&nbsp;

## Containerize Frontend
- Change directory to ‘frontend-react-js/`
```sh
cd frontend-react-js/
```

- Next, I ran an NPM install before building the frontend container because it needs to copy the contents of node modules
```sh
npm i
```

- Change back to the main directory (aws-bootcamp-cruddur-2023)
```sh
cd ..
```

- Now create a docker file at `front-end-react-js/Dockerfile`.
- Click on `frontend-react-js` directory in Gitpod, right click and choose NEW FILE.
- Name the new file `Dockerfile`.
- Copy and paste the following code into the newly created ‘Dockerfile`:
```sh
FROM node:16.18
ENV PORT=3000
COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

![npm install](/_docs/assets/npm.jpg)



&NewLine;
&NewLine;
&nbsp;

- Created a `docker-compose.yml` file at root of my project (main).
&NewLine;
&NewLine;
&nbsp;

This .yml file allows for the running of multiple containers at the same time.
&NewLine;
&NewLine;
&nbsp;

Docker Compose is a multi-container deployment with Infrastructure As Code (IAC) that makes it much easier to run containers that need to run together logically.
&NewLine;
&NewLine;
&nbsp;

The standard way of using Docker is with a Docker file, but it is not good for running multiple parts of an application.
&NewLine;
&NewLine;
&nbsp;

Docker came up with Docker Compose as a solution to running multiple parts of a project.
&NewLine;
&NewLine;
&nbsp;

`docker-compose.yml` file:
```sh
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```

- run `docker-compose up`
```sh
Docker-compose up
```

- The frontend container(s) is on port 3000
- The backend container(s) is on port 4567
- Make sure both are unlocked.

 #### Right click this file and click, "Compose up"
![docker-compose up](/_docs/assets/composeup.png)

### Run docker compose
![docker-compose](/_docs/assets/compose.jpg)

 ### Make sure to unlock the ports for frontend and backend, and try launching the Frontend
![docker-compose](/_docs/assets/ports.jpg)

 ### Moment of truth! Lets open the Frontend to see if it works!

 ![app-work](/_docs/assets/app.jpg)

 ## Adding DynamoDB Local and Postgres

-	DynamoDb Local and Postgres will be used in future labs.
-	I am bringing them in as containers to be referenced externally.
-	I added both DynamoDB Local and Postgre to my `docker-compose.yml` file.
-	First, I added the DynamoDB Local code at the bottom of the docker-compose file just ablove the comment “the name flag is a hack to change the default prepend folder”

  
&NewLine;
&NewLine;
&nbsp;

NOTE:  Exclude `services:` if you are putting the DynamoDB Local code into a pre-existing .yml file that already has `services:` listed in the file code. If not, leave it in.

-	DynamoDB Local:
 
```sh
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

-	Next, I added the following Postgres .yml code to my docker-compose file right below the last line of the newly added DynamoDB code.
```sh
db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
```



-	Then, I added the following code to docker-compose to the very end of the file just below “name:cruddur”:
```sh
volumes:
  db:
    driver: local
```
NOTE: 
-	`driver: local` in the db volume is referencing the database volume ` db:/var/lib/postgresql/data` in the Postgres code. I’ll be coyping the database data and storing it on my local machine.
-	You can see from the ` ./docker/dynamodb:/home/dynamodblocal/data` code in DynamoDB Local that `./docker/dynamodb` maps to `/home/dynamodblocal/data`.
&NewLine;
&NewLine;
&nbsp;

![app-work](/_docs/assets/installdb.jpg)

&NewLine;
&NewLine;
&nbsp;

-	Now to run the docker-compose.yml file with docker `compose up` to run DynamoDB Local and Postgres to see how they interact with the other services in the file.
-	Right-click on  `docker-compose.yml`
-	Click on `Compose Up` to run the services


&NewLine;
&NewLine;
&nbsp;

-	Go to PORTS tab in Gitpod terminal that ran DynamoDB and Postgres and unlock ports 4567, 5432, and 8000 to make them Public and accessible. 
-	DnyamoDB Local is running on port 8000 and Postgres is running on port 5432.
-	Don’t open port 38487 since it has to do with Gitpod and not with DynamoDB and PostGres.


![Correct Unlocked Ports](/_docs/assets/unlock.jpg)

-	Open a new terminal and run the command ‘aws sts get-caller-identity` to confirm AWS CLI is still installed.
-	Now to check that I can interact with the database using database task code from the challenge “100 Days of Cloud” at  https://github.com/100DaysOfCloud/challenge-dynamodb-local
-	I chose List Tables task with the following code:
```sh
aws dynamodb list-tables --endpoint-url http://localhost:8000
```

-	Next the Postgres client is installed into Gitpod.
-	Place the following Postgres driver code into `gitpod.yml` right after “cd $THEIA….”  
```sh
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

-	Run each of the 4 lines of Postgres code SEPARATELY in the Bash Terminal.
&NewLine;
&NewLine;
&nbsp;


-	The code added gpg key to read from a remote repository and installed the Debian package.
-	I tried to connect to client with the command ‘psql’, but I received the error ` connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: No such file or directory`
-	I shut down docker  wtth `Compose Down’ and spun it back up with `Compose Up`
-	Checked the ports to make sure they are open and confirmed they are open to public.
-	Commit changes to `gitpod.yml`, exited Gitpod, then reopened Gitpod to make sure it recognizes added 4 lines of Postgres code.
-	I reloaded `docker-compose.yml` using the command ~docker compose up``
```sh
docker compose up
```

-	Added `PostgreSQL` extension to Gitpod and to the file `gitpod.yml`

![Add PostgreSQL Ext to gitpod.yml](/_docs/assets/add-postgresql-to-gitpodyml.png)

-	Proof PostgresSQL extension added to gitpod.yml file

![PostgreSQL in gitpod.yml](/_docs/assets/psqlingitpod.jpg)


&NewLine;
&NewLine;
&nbsp;

### Connect to Server Via DATABASE Explorer
-	Before continuing my attempt at connecting to the database server by command line, I will connect by Gitpod `DATABASE`.
-	Click on DATABASE icon on left vertical taskbar.
-	Click `+` or `create connection` to create new connection to database server.
-	I made a successful connection to the database server.
-	The database is empty and will be populated in Week 2.

![success](/_docs/assets/psqlsuccess.jpg)

&NewLine;
&NewLine;
&nbsp;

-	Now I continue with trying to connect to the database server via command line.

-	Again, run the 4 lines of gitpod.yml Postgre coded SEPARATELY in Terminal.


```sh
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

-	Still unable to connect to the database with command `psql`.
-	Need to provide local host option with “psql’

```sh
psql --host localhost
```

-	I was prompted for password, but the password didn’t work and I received error that “psql: error: connection to server at "localhost" (::1), port 5432 failed: FATAL:  password authentication failed for user "gitpod"”
-	Tried again using the command:
```sh
psql -Upostgres --host localhost
```
-	Now I can access postgres
  
![psqlUpostgress](/_docs/assets/psqla1.jpg)



### Launch an Amazon free-tier eligible EC2 instance and install Docker

1. Create an EC2 instance
![EC2running](/_docs/assets/dockerec2.jpg)

2. Install docker
` sudo yum install docker -y `

3. Start the Docker service
`sudo service docker start`

Output:

```sh
[ec2-user@ip-172-31-27-244 ~]$ sudo service docker start
Redirecting to /bin/systemctl start docker.service
[ec2-user@ip-172-31-27-244 ~]$ date
Fri Feb 24 18:13:02 UTC 2023
```

4. Check the status of Docker service

Check status:
`systemctl status docker`

Output:
```sh
[ec2-user@ip-172-31-27-244 ~]$ systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since Fri 2023-02-24 18:12:56 UTC; 18s ago
     Docs: https://docs.docker.com
  Process: 3541 ExecStartPre=/usr/libexec/docker/docker-setup-runtimes.sh (code=exited, status=0/SUCCESS)
  Process: 3540 ExecStartPre=/bin/mkdir -p /run/docker (code=exited, status=0/SUCCESS)
 Main PID: 3544 (dockerd)
    Tasks: 7
   Memory: 20.8M
   CGroup: /system.slice/docker.service
           └─3544 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --default-ulimit nofile=32768:65536

Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.307285735Z" level=info msg="ClientConn switching balancer to \"pick_first...dule=grpc
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.345070523Z" level=warning msg="Your kernel does not support cgroup blkio weight"
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.345497426Z" level=warning msg="Your kernel does not support cgroup blkio ...t_device"
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.345939221Z" level=info msg="Loading containers: start."
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.528538493Z" level=info msg="Default bridge (docker0) is assigned with an ... address"
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.575575709Z" level=info msg="Loading containers: done."
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.597059083Z" level=info msg="Docker daemon" commit=a89b842 graphdriver(s)=...=20.10.17
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.597530250Z" level=info msg="Daemon has completed initialization"
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal systemd[1]: Started Docker Application Container Engine.
Feb 24 18:12:56 ip-172-31-27-244.eu-central-1.compute.internal dockerd[3544]: time="2023-02-24T18:12:56.623407852Z" level=info msg="API listen on /run/docker.sock"
Hint: Some lines were ellipsized, use -l to show in full.
[ec2-user@ip-172-31-27-244 ~]$

```


6. Before proceeding further we have to check if we are able to execute Docker commands as non-root user. To verify this, let's execute the docker info command as below:
` docker info `

Output:

```sh
[ec2-user@ip-172-31-27-244 ~]$ docker info
Client:
 Context:    default
 Debug Mode: false

Server:
ERROR: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/info": dial unix /var/run/docker.sock: connect: permission denied
errors pretty printing info

```

As expected, it did not work.

7. So, we now add our user _ec2_user_ to the _Docker_ group.

`  sudo groupadd docker `

_Note:_ if the _Docker_ group does not exist, we can create it using the below command and then add the user to the group:

` sudo groupadd docker `

For this to take effect, we can either restart our EC2 instance or refresh the group list manually, using the below command:

` sudo usermod -aG docker ec2-user `

_Note:_ If all goes well, we don't expect the command to give a terminal output.

8. Execute the `docker info ` command again.

```sh
[ec2-user@ip-172-31-27-244 ~]$ docker info
Client:
 Context:    default
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 20.10.17
 Storage Driver: overlay2
  Backing Filesystem: xfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 9cd3357b7fd7218e4aec3eae239db1f68a5a6ec6
 runc version: 5fd4c4d144137e991c4acebb2146ab1483a97925
 init version: de40ad0
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 5.10.165-143.735.amzn2.x86_64
 Operating System: Amazon Linux 2
 OSType: linux
 Architecture: x86_64
 CPUs: 1
 Total Memory: 964.8MiB
 Name: ip-172-31-27-244.eu-central-1.compute.internal
 ID: VQRN:EH7L:TK32:FZHR:35WC:LTIA:VVZ4:MYZY:SQFZ:J4FI:OF6K:ADB2
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false
```

### Deploy a container image

1. Let's create an _index.html_ that will be hosted on our Apache container

```html
<html>
<head>
        <title>
                Tanushree's Docker test
        </title>
</head>
<body>
        <b>Hello World! This is my first Docker run!</b>
<body>
</html>
```

2. Let's create our first ever **Dockerfile**
This is a simple Dockerfile, that is pulling the httpd:2.4 image from Dockerhub, and copying  _index.html_ from our EC2 file system to the apache docs hosting path.

```sh
FROM httpd:2.4
COPY index.html /usr/local/apache2/htdocs/index.html
```

3. Now we build our Docker image based on this _Dockerfile_

`docker build -t <name-to-be-assigned-to-the-image> <path to Dockerfile>`

-t : defines the <name>:<tag> for the docker image. (If no tag is defined, the default tag _latest_ gets assigned)
(.)period : the path where Dockerfile is present (period represents the current directory)

Example:

```sh
[ec2-user@ip-172-31-27-244 ~]$ docker build -t apache-docker-example .
Sending build context to Docker daemon  13.31kB
Step 1/2 : FROM httpd:2.4
2.4: Pulling from library/httpd
bb263680fed1: Pull complete
9e8776e4b876: Pull complete
f506d7aab652: Pull complete
05289ee4f284: Pull complete
b7f64f2f8747: Pull complete
Digest: sha256:db2d897cae2ad67b33435c1a5b0d6b6465137661ea7c01a5e95155f0159e1bcf
Status: Downloaded newer image for httpd:2.4
 ---> 3a4ea134cf8e
Step 2/2 : COPY index.html /usr/local/apache2/htdocs/index.html
 ---> ff35af99cd38
Successfully built ff35af99cd38
Successfully tagged apache-docker-example:latest
[ec2-user@ip-172-31-27-244 ~]$
```	
	
4. Verify if the image got created
`docker images`

This command lists the details of all available Docker images in our local
Example:

```sh
[ec2-user@ip-172-31-27-244 ~]$ docker images
REPOSITORY              TAG       IMAGE ID       CREATED          SIZE
apache-docker-example   latest    ff35af99cd38   29 seconds ago   145MB
httpd                   2.4       3a4ea134cf8e   2 weeks ago      145MB
hello-world             latest    feb5d9fea6a5   17 months ago    13.3kB
centos                  latest    5d0da3dc9764   17 months ago    231MB
[ec2-user@ip-172-31-27-244 ~]$

```
	
5. Run the docker container from the docker image created in step 3
	
`docker run -d --name <assign-name-to-container> -p <local-port>:<remote-port> <image-name>`

	-d : to run as a daemon process
	-p: for port mapping. mapping local port 80, to docker internal port 80 

```sh
[ec2-user@ip-172-31-27-244 ~]$ docker run -d --name helloworld -p 80:80 apache-docker-example
5c8e630daf7ad4843bccddf27eb44c7c4711ec02e9dfdc77de8bc25bbf75e0b4
[ec2-user@ip-172-31-27-244 ~]$

```
	
6. Check running Docker processes
	
`docker ps`
	
Lists the running docker process and associated details.
Example:

```sh
[ec2-user@ip-172-31-27-244 ~]$ docker ps
CONTAINER ID   IMAGE                   COMMAND              CREATED       STATUS       PORTS                               NAMES
5c8e630daf7a   apache-docker-example   "httpd-foreground"   2 hours ago   Up 2 hours   0.0.0.0:80->80/tcp, :::80->80/tcp   helloworld
[ec2-user@ip-172-31-27-244 ~]$
```

![docker_deployment_complete](/_docs/assets/week1_docker_image_stretch.png)

7. Launch the EC2 public I.P address on port 80 in a web browser to verify if apache was successfully installed and see if our index.html was hosted. 

![apache_container_running](/_docs/assets/container%20run.jpg)

8. To stop a docker process
`docker stop <docker-container-name>`
	
Example: `docker stop alexis_docker`
	
### Push container image to Docker Registry

i.  Create a Dockerhub account on the Docker [website](https://hub.docker.com/)

ii. Login to dockerhub from your EC2 instance 
	` docker login `
    Enter the Docker user name and password created in (i) when prompted. This will store your credentials in /home/ec2-user/.docker/config.json.
    _Note:_ it is not a recommended practice to store credentials plainly, rather in a secrets store. (I still need to study up how to configure this with AWS Secrets Manager. )

iii. Tag the docker image we wish to push to Dockerhub against our account
	`docker tag <image-name>:<assigned-tag> <dockerhub-username>/<name-you-wish-to-assign>:<tag-you-wish-to-assign `

     Example: `docker tag apache-docker-example:latest akhlab/apache-docker-example:latest`
    
 iv. Push the image to Dockerhub
 	`docker push <dockerhub-username>/<name-you-assigned-in-step-iii>:<tag-you-assigned-in-step-iii`
   
     Example: `docker push akhlab/apache-docker-example:latest`

![docker image](/_docs/assets/upload.jpg)

v. Verify the image availability in your DockerHub account

![docker image](/_docs/assets/dockerhubrepo.jpg)




## Reference Link
1. [Watch AB's week 1 stream](https://www.youtube.com/watch?v=zJnNe5Nv4tE&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=22) :white_check_mark:
2. [Watch Grading Homework Summaries](https://www.youtube.com/watch?v=FKAScachFgk&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=25) :white_check_mark:
3. [Watch Chirag's Week 1 - Spending Considerations](https://www.youtube.com/watch?v=OAMHu1NiYoI&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=24) :white_check_mark:
4. [Watch Remember to Commit Your Code](https://www.youtube.com/watch?v=b-idMgFFcpg&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=23) :white_check_mark:
5. [Watch Ashish's Week 1 - Container Security Considerations](https://www.youtube.com/watch?v=OjZz4D0B-cA&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=25) :white_check_mark:
6. [Watch Containerize Application (Dockerfiles, Docker Compose)](https://www.youtube.com/watch?v=zJnNe5Nv4tE&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=22) :white_check_mark:
7. [Document the Notification Endpoint for the OpenAI Document](https://www.youtube.com/watch?v=k-_o0cCpksk&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=27) :white_check_mark:
8. [Write a Flask Backend Endpoint for Notifications](https://www.youtube.com/watch?v=k-_o0cCpksk&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=27) :white_check_mark:
9. [Write a React Page for Notifications](https://www.youtube.com/watch?v=k-_o0cCpksk&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=27) :white_check_mark:
10. [Run DynamoDB Local Container and ensure it works](https://www.youtube.com/watch?v=CbQNMaa6zTg&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=28) :white_check_mark:
11. [Run Postgres Container and ensure it works](https://www.youtube.com/watch?v=CbQNMaa6zTg&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=28) :white_check_mark:
12. Cloud careers [livestream](https://www.youtube.com/watch?v=E0haz6mymxY) :white_check_mark:



















