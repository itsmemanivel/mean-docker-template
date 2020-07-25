# MEAN Stack using Docker

### MongoDB - Express - Angular - NodeJS

## About Docker Compose File
The main focus of this project to show case the possible way to run a real application (Mean stack) using docker.

we have considered 3 scenarios:

1. **Using 2 containers** ([docker-compose.yml](/docker-compose.yml)) 

   
    * express : To host Frontend (Angular) and backend api (expressjs) together
    * database: To host MongoDB 

##### Note: If in above case we are using MongoDB as managed service  then we will require only one container.



```dockerfile
version: "3.8" # specify docker-compose version

# Define the services/containers to be run
services:
  express: #name of the second service
    build: # specify the directory of the Dockerfile
      context: .
      dockerfile: dockerfile
    container_name: mean_angular_express
    ports:
      - "3000:3000" #specify ports forewarding
      # Below database enviornment variable for api is helpful when you have to use database as managed service
    environment:
      - SECRET=Thisismysecret
      - MONGO_DB_USERNAME=admin-user
      - MONGO_DB_PASSWORD=admin-password
      - MONGO_DB_HOST=database
      - MONGO_DB_PORT=
      - MONGO_DB_PARAMETERS=?authSource=admin
      - MONGO_DB_DATABASE=mean-contacts
    links:
      - database

  database: # name of the third service
    image: mongo:latest # specify image to build container from
    container_name: mean_mongo
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin-user
      - MONGO_INITDB_ROOT_PASSWORD=admin-password
      - MONGO_DB_USERNAME=admin-user1
      - MONGO_DB_PASSWORD=admin-password1
      - MONGO_DB=mean-contacts
    volumes:
      - ./mongo:/home/mongodb
      - ./mongo/init-db.d/:/docker-entrypoint-initdb.d/
      - ./mongo/db:/data/db
    ports:
      - "27017:27017" # specify port forewarding
```

2. **Using 4 containers** ([docker-compose.nginx.yml](/docker-compose.nginx.yml))
   
    * angular: Application's frontend (Angular) 
    * express: Application's Rest services (expressjs)
    * database: Application database: MongoDB
    * nginx: As laod balancer, also expose UI and API on same ports

##### Note: If in above case we are using MongoDB as managed service  then we will require only one container.
```dockerfile
version: "3.8" # specify docker-compose version

# Define the services/containers to be run
services:
  angular: # name of the first service
    build: frontend # specify the directory of the Dockerfile
    container_name: mean_angular
    ports:
      - "4000:4000" # specify port forewarding
    environment:
      - NODE_ENV=dev

  express: #name of the second service
    build: api # specify the directory of the Dockerfile
    container_name: mean_express
    ports:
      - "3000:3000" #specify ports forewarding
      # Below database enviornment variable for api is helpful when you have to use database as managed service
    environment:
      - SECRET=Thisismysecret
      - MONGO_DB_USERNAME=admin-user
      - MONGO_DB_PASSWORD=admin-password
      - MONGO_DB_HOST=database
      - MONGO_DB_PORT=
      - MONGO_DB_PARAMETERS=?authSource=admin
      - MONGO_DB_DATABASE=mean-contacts
    links:
      - database

  database: # name of the third service
    image: mongo:latest # specify image to build container from
    container_name: mean_mongo
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin-user
      - MONGO_INITDB_ROOT_PASSWORD=admin-password
      - MONGO_DB_USERNAME=admin-user1
      - MONGO_DB_PASSWORD=admin-password1
      - MONGO_DB=mean-contacts
    volumes:
      - ./mongo:/home/mongodb
      - ./mongo/init-db.d/:/docker-entrypoint-initdb.d/
      - ./mongo/db:/data/db
    ports:
      - "27017:27017" # specify port forewarding

  nginx: #name of the fourth service
    build: loadbalancer # specify the directory of the Dockerfile
    container_name: mean_nginx
    ports:
      - "80:80" #specify ports forewarding
    links:
      - express
      - angular
```

3. **Development Mode** ([docker-compose.debug.yml](/docker-compose.debug.yml))

    It will run 3 containers which are required for development.

  ```dockerfile
  version: "3.8" # specify docker-compose version

# Define the services/containers to be run
services:
  angular: # name of the first service
    build: # specify the directory of the Dockerfile
      context: ./frontend
      dockerfile: debug.dockerfile
    container_name: mean_angular
    volumes:
      - ./frontend:/frontend
      - /frontend/node_modules
    ports:
      - "4200:4200" # specify port forewarding
      - "49153:49153"
    environment:
      - NODE_ENV=dev

  express: #name of the second service
    build: # specify the directory of the Dockerfile
      context: ./api
      dockerfile: debug.dockerfile
    container_name: mean_express
    volumes:
      - ./api:/api
      - /api/node_modules
    ports:
      - "3000:3000" #specify ports forewarding
    environment:
      - SECRET=Thisismysecret
      - NODE_ENV=development
      - MONGO_DB_USERNAME=admin-user
      - MONGO_DB_PASSWORD=admin-password
      - MONGO_DB_HOST=database
      - MONGO_DB_PORT=
      - MONGO_DB_PARAMETERS=?authSource=admin
      - MONGO_DB_DATABASE=mean-contacts
    links:
      - database

  database: # name of the third service
    image: mongo # specify image to build container from
    container_name: mean_mongo
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin-user
      - MONGO_INITDB_ROOT_PASSWORD=admin-password
      - MONGO_DB_USERNAME=admin-user1
      - MONGO_DB_PASSWORD=admin-password1
      - MONGO_DB=mean-contacts

    volumes:
      - ./mongo:/home/mongodb
      - ./mongo/init-db.d/:/docker-entrypoint-initdb.d/
      - ./mongo/db:/data/db
    ports:
      - "27017:27017" # specify port forewarding

  ```
   

## About Application

MEAN (full) stack application which comprises of MongoDB, ExpressJS, Angular and NodeJS. MongoDB is database, NodeJS and ExpressJS are for rest apis, and Angular is for front end.

Docker Compose can be used to create separate containers (and host them) for each of the stacks in a MEAN stack application. MEAN is the acronym for MongoDB Express Angular & NodeJs

## Angular (10.0.2)

In MEAN stack A stands for Angular, fronend of this project is developed in Angular.

It contains sample for below:

 1. User Registration
 2. Login
 3. Profile
 4. A complete CRUD example for Contact

Also, It has sample code for Auth guard, services, http interceptors, resolver and JWT  imaplmenation

For folder structure details refer this link: [Frontend Folder Structure](/docs/angular-frontend-structure.md)

##### [Dockerfile for Production](/frontend/Dockerfile)
##### [Dockerfile for Development](/frontend/debug.dockerfile)

## Expressjs (4.17.1)

In MEAN stack, E stands for Expressjs, all rest services are developed using express js.

It constains sample for:

1. Mongodb connection and schema validation using Mongoose
2. JWT implementation for Authorization
3. API routing
4. User registration & login APIs
5. Complete CRUD exmaple for Contact

For folder structure details refer this link: [API Folder Structure](/docs/expressjs-api-structure.md)

##### [Dockerfile for production](/api/Dockerfile)
##### [Dockerfile for development](/api/debug.dockerfile)

## Mongo DB

We are using Mongodb as database. MongoDB is a cross-platform document-oriented database program. Classified as a NoSQL database program, MongoDB uses JSON-like documents with optional schemas.

##### [Seed data script](/mongo/init-db.d/01.Seed.sh)
##### [Database user creation script](/mongo/init-db.d/02.Users.sh)

## NGINX

###### Note: only if you are using docker.

We have uses NGINX loadbalancer in case if there is a requirement that frontend and api need to be exposed on same port.
 For configutration please check [nginx.conf](/loadbalancer/nginx.conf)

### Loadbalancer (nginx) [Dockerfile](/api/loadbalancer)


****
## How to run project

### Using Docker

1. To run the project run command: `docker-compose -f 'compose-file-name' up`

     * docker-compose.yml -> To use 2 containers (explained above) 
     * docker-compose.nginx.yml -> To use 4 containers (explained above)
     * docker-compose.debug.yml -> To run Angular and Expressjs in Development mode (watch mode)
2. Use `http://localhost` or `http://localhost:4000` , while debugging, use `http://localhost:4200` for frontend and `http://localhost:3000` for api.

3. Sign up for new user and login

<hr>

#### Without Docker

##### Prerequisite

1. Install latest [Node js ](https://nodejs.org/en/)
2. Install Nodemon as global package (To run exprerssjs in development mode)
   `npm install -g nodemon`
3. Optional Install Angular CLI
   `npm install -g @angular/cli`
4. Install Mongodb locally or [Signup](https://www.mongodb.com/atlas-signup-from-mlab?utm_source=mlab.com&utm_medium=referral&utm_campaign=mlab%20signup&utm_content=blue%20sign%20up%20button) for a free managed account
5. Before running the project make sure that you are able to connect MongoDb , you can use [Robo 3T](https://robomongo.org/download) for it

#### Running the Project

1. Update connection string in config.json file. (in api folder)
2. Navigate to api folder and install all dependencies.
   RUN `npm i`
3. Navigate to forndend folder and install all dependencies.
   RUN `npm i`
4. From same folder (frontend) RUN `npm run dev-server` it will run both API and frontend
