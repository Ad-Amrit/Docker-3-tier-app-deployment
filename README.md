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
![Screenshot (200)](https://github.com/user-attachments/assets/fc2e5d16-c6a6-4bb9-82ba-75a48668df65)
![Screenshot (202)](https://github.com/user-attachments/assets/ceb32b3d-77cc-4b79-8f5a-d0f23670dc99)
![Screenshot (203)](https://github.com/user-attachments/assets/e11cf192-c09a-4e93-980d-5cff51b7a227)

