
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
