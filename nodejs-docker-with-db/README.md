
###  Creating the Dockerfile

In the root directory of the application, create a new Dockerfile.

```
touch Dockerfile
```

Open the newly created Dockerfile in your favorite editor and edit it.

```
FROM node:7.7.2-alpine
WORKDIR /usr/app
COPY package.json .
RUN npm install --quiet
COPY . .
```

If you run docker build ., you will see something similar to the following:

```
Sending build context to Docker daemon 249.3 kB
Step 1/1 : FROM node:7.7.2-alpine
7.7.2-alpine: Pulling from library/node
709515475419: Pull complete
1a7746e437f7: Pull complete
662ac7b95f9d: Pull complete
Digest: sha256:6dcd183eaf2852dd8c1079642c04cc2d1f777e4b34f2a534cc0ad328a98d7f73
Status: Downloaded newer image for node:7.7.2-alpine
 ---> 95b4a6de40c3
Successfully built 95b4a6de40c3
```

At this point, you now have an image created, and running docker images will show you the images you have available:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
node                7.7.2-alpine        95b4a6de40c3        6 weeks ago         59.2 MB
```

You can now run ```docker build .``` again and see the results:

```
Sending build context to Docker daemon 249.3 kB
Step 1/5 : FROM node:7.7.2-alpine
  ---> 95b4a6de40c3
Step 2/5 : WORKDIR /usr/app
 ---> e215b737ca38
Removing intermediate container 3b0bb16a8721
Step 3/5 : COPY package.json .
 ---> 930082a35f18
Removing intermediate container ac3ab0693f61
Step 4/5 : RUN npm install --quiet
 ---> Running in 46a7dcbba114
 ### NPM MODULES INSTALLED ###
 ---> 525f662aeacf
 ---> dd46e9316b4d
Removing intermediate container 46a7dcbba114
Step 5/5 : COPY . .
 ---> 1493455bcf6b
Removing intermediate container 6d75df0498f9
Successfully built 1493455bcf6b

```



### Docker Compose Services

Create the file docker-compose.yml:

```
touch docker-compose.yml
```

Open your docker-compose.yml file in your editor and copy/paste the following lines:

```
version: '2'
services:
  web:
    build: .
    command: npm run dev
    volumes:
      - .:/usr/app/
      - /usr/app/node_modules
    ports:
      - "3000:3000"
    depends_on:
      - postgres
    environment:
      DATABASE_URL: postgres://todoapp@postgres/todos
  postgres:
    image: postgres:9.6.2-alpine
    environment:
      POSTGRES_USER: todoapp
      POSTGRES_DB: todos
```



#### The web service

The first directive in the web service is to build the image based on our Dockerfile. This will recreate the image we used before, but it will now be named according to the project we are in, nodejsexpresstodoapp. After that, we are giving the service some specific instructions on how it should operate:

* command: npm run dev - Once the image is built, and the container is running, the npm run dev command will start the application.

* volumes: - This section will mount paths between the host and the container.

* .:/usr/app/ - This will mount the root directory to our working directory in the container.

* /usr/app/node_modules - This will mount the node_modules directory to the host machine using the buildtime directory.

* environment: - The application itself expects the environment variable DATABASE_URL to run. This is set in db.js.

* ports: - This will publish the container's port, in this case 3000, to the host as port 3000.

The DATABASE_URL is the connection string. postgres://todoapp@postgres/todos connects using the todoapp user, on the host postgres, using the database todos.

#### The Postgres service

Like the NodeJS image we used, the Docker Store has a prebuilt image for PostgreSQL. Instead of using a build directive, we can use the name of the image, and Docker will grab that image for us and use it. In this case, we are using postgres:9.6.2-alpine. We could leave it like that, but it has environment variables to let us customize it a bit.

* environment: - This particular image accepts a couple environment variables so we can customize things to our needs. POSTGRES_USER: todoapp - This creates the user todoapp as the default user for PostgreSQL. POSTGRES_DB: todos - This will create the default database as todos.


### Running The Application

Now that we have our services defined, we can build the application using ```docker-compose up```

This tells us that you can access the application using localhost:3000/todos on the host machine.
