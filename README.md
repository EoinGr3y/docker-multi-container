# docker-multi-container
Basic multi container goal application from [udemy docker course](https://www.udemy.com/course/docker-kubernetes-the-practical-guide/)

This application is composed of 3 components;
- Database - mongodb for storing/retrieval goals
- Back-end - node express API app
- Front-end - react app

## Prerequisites
Note that you must have a docker network named `goals-net` present to run this application. You can create it with the command `docker network create goals-net`

## Database
This uses the [official mongo docker image](https://hub.docker.com/_/mongo). 

Run with the command 
`docker run --name mongodb -v data:/data/db --rm -d --network goals-net -e MONGO_INITDB_ROOT_USERNAME=<username> -e MONGO_INITDB_ROOT_PASSWORD=<password> mongo`

Note that the volume `data:/data/db` ensures that the database contents are persisted using a volume, meaning they are not wiped on container stop.

## Back-end
This is a simple express application that uses mongoose to connect to the database. 

Build the image with the command `docker build -t goals-node .`

Run with the command `docker run --name goals-backend -v <filePath>/backend/:/app -v logs:/app/logs -v /app/node_modules -e MONGODB_USERNAME=<username> -d --rm --network goals-net -p 80:80 goals-node`

Note that the app is run using nodemon to provide live reloading during development. The named volume `<filePath/backend/:/app` ensures that the container code is updated on code change.

## Front-end
This is a basic react app for adding/removing goals. Default url is http://localhost:3000/

Build the image with the command `docker build -t goals-react .`

Run with the command `docker run -v <filePath>/frontend/src:/app/src --name goals-frontend --network goals-net --rm -p 3000:3000 -it goals-react`

