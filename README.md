# Docker-3-tier-app-deployment
3-tier App Deployment With Docker
First, clone the project 
git clone https://github.com/MonkeyDmagnas/Demo-wanderlust.git
OR
git clone https://github.com/Ad-Amrit/Docker-3-tier-app-deployment.git

Now, We need to build our image with the docker 

- Goto Frontend Directory

```jsx
cd /path/to/frontend/
```

- Create a Dockerfile for frontend

```jsx
FROM node:21

WORKDIR /app

COPY . .

RUN npm install \
 npm run build

COPY .env.local .env.local

EXPOSE 5173

CMD ["npm", "run", "dev","--","--host"]

```

- After creating Dockerfile, Run command

```jsx
docker build -t frontend:<tag_name> .
```
Node:If you do not add any tag name, then it will be latest by default

- Now, Goto Backend Folder and edit .env file

```jsx
PORT=5000
MONGODB_URI="mongodb://(mongo_database_container_name)/wanderlust"
CORS_ORIGIN="http://localhost:5173"
FRONTEND_URL="http://localhost:5173"
NODE_ENV=Development
```

Note: Replace (mongo_database_container_name) with the name mongo or your specific container name.

- Now, Create Dockerfile for Backend

```jsx
FROM node:21

WORKDIR /app

COPY . .

RUN npm i

COPY .env .env

EXPOSE 5000

CMD ["npm","start"]
```

- Now build backend Image with command:

```jsx
docker build -t backend:<tag_name> .
```

- Create docker network with docker command:

```jsx
docker network create wanderlust
```

- Run the frontend container with command:

```jsx
docker run -idt -p 5173:5173 --name frontend --network wanderlust frontend:latest
```

- Run the backend container with command:

```jsx
docker run -idt -p 5000:5000 --name backend --network wanderlust backend:latest
```

- Run mongo database container with command

```jsx
docker run -idt -p 27017:27017 --name mongo(give_specific_name_that you_changed_in_environment_file) --network wanderlust mongo
e.g. sudo docker run -idt -p 27017:27017 --name mongo --network wanderlust mongo:latest
OR
docker pull mongo
docker run -idt -p 27017:27017 --name mongo --network wanderlust mongo
```

- Goto Backend folder

```jsx
cd ./backend/data
#Copy sample_post.json file to mongo database container
docker cp sample_posts.json mongo:/data/
#goto mongo database container
docker exec -it <mongo_container_name> bash
#insert data in database with following command:
mongoimport --db wanderlust --collection posts --file ./data/sample_posts.json --jsonArray
#get out of container 
exit
```

- After following each steps your project should display like this:
![Screenshot (200)](https://github.com/user-attachments/assets/e48bc798-f87c-4560-9fbd-b8a1ac6ee797)
![Screenshot (202)](https://github.com/user-attachments/assets/4948ee80-4127-45e0-9da2-09783655c024)
![Screenshot (203)](https://github.com/user-attachments/assets/14986cd6-a0ca-4371-86fe-7ef325ba3240)


