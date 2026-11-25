1. Clonit el repo
Mel loul clonit el repo mta3 el projet w d5alt lel dossier :

git clone https://github.com/wiem-kb/MyAppReact.git
cd MyAppReact
fih zouz dossiers : `Server`w `Client`
2. 3malt el Dockerfile mta3 el server
Fil dossier `Server` 3malt fichier `Dockerfile` w 7attit fih el code hedha :

FROM node:lts-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 9000
CMD ["npm", "start"]

Ba3d buildit l'image mta3 el server b el commande hedhi :

docker build -t mern-server ./Server


3. 3malt el Dockerfile mta3 el client
Fil dossier `Client` 3malt zeda `Dockerfile` w 7attit fih :

FROM node:lts-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
RUN npm install -g serve
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]

*W buildit l'image mta3 el client :

docker build -t mern-client ./Client


4. 3malt el réseau
*3malt réseau bech les conteneurs ynajmou ycomminiquiw m3a b3adhhom :
```
docker network create mern-network
```

5. Lancit MongoDB
*Lancit conteneur MongoDB w rbattou bel réseau :
```
docker run -d --name mongodb --network mern-network mongo
```

6. Lancit el server w el client

docker run -d --name server --network mern-network -p 9000:9000 mern-server
docker run -d --name client --network mern-network -p 3000:3000 mern-client

*Thabbatet b `docker ps` elli les 3 conteneurs ye5dmou w 7allit `http://localhost:3000` fil navigateur L9it fih "API is running".

7.3malt el docker-compose
Fil racine mta3 el projet 3malt fichier `docker-compose.yml` w 7attit fih kima kal l ennonce yheb:

services:
  mongodb:
    image: mongo
    container_name: mongodb
    networks:
      - mern-network

  server:
    build: ./Server
    container_name: server
    ports:
      - "9000:9000"
    environment:
      - MONGO_URI=mongodb://mongodb:27017/merndb
    depends_on:
      - mongodb
    networks:
      - mern-network

  client:
    build: ./Client
    container_name: client
    ports:
      - "3000:3000"
    depends_on:
      - server
    networks:
      - mern-network

networks:
  mern-network:
    driver: bridge

*lancit kol chay b docker-compose :

docker compose up --build


8. Pushit les images 3al Docker Hub

docker tag mern-server bayramalaya/mern-server:latest
docker tag mern-client bayramalaya/mern-client:latest
docker push bayramalaya/mern-server:latest
docker push bayramalaya/mern-client:latest


9. 3malt el pipeline Jenkins
Pushit el projet 3al GitHub, w 3malt fichier `Jenkinsfile` fih 4 étapes : **Checkout** (yjib el code mel GitHub), **Build images**, **Push Docker Hub** w **Deploy** (docker compose). Ba3d fi Jenkins 3malt job Pipeline rbattou bel repo mtaa3 GitHub w lancit el build.



