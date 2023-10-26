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

