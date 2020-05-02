# Dockerizando uma App Node.JS

## Dockerfile - Boas Práticas
[Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)


## Dockerizando uma App Node.JS

Vamos criar um Docker file

```bash
FROM node:alpine

WORKDIR /usr/scr/app

COPY ./crud-node-postgres/package*.json ./
RUN npm install

COPY ./crud-node-postgres/ .

RUN npm install -g bower
COPY crud-node-postgres/bower*.json ./
RUN bower install --allow-root

EXPOSE 3030

CMD [ "node", "server.js" ]
```

Agora vamos criar um `docker-compose.yml` para subir nossa App

```bash
version: '3'

services:
  nodejs:
    build: .
    image: paulorcvieira/app-node-dockerizando:latest
    volumes:
      - .:/workspace
    ports:
      - "3030"
    depends_on:
      - db
  db:
    image: postgres
    restart: always
    environment:
      POSTGRES_PASSWORD: password
    ports:
      - "5432"
    volumes:
      - ./crud-node-postgres/database:/var/lib/postgresql/data
```

Vamos criar um arquivo `.dockerignore` dentro de `/crud-node-postgres`, para ignorar alguns arquivos durante a cópia para dentro do nosso `workdir`, mesmo sistema do *.gitignore* do github

```bash
node_modules
public/bower_components
npm-debug.log
```

Agora podemos rodar nosso comando para subir a imagem

```bash
$ docker-compose build
$ docker-compose up -d
```
Ou
```bash
$ docker-compose up -d --build
```

Feito isso podemos criar a tabela `crud-node` no pgAdmin, e após isso vamo rodar o comando a seguir para que ele rode o `service nodejs` e dentro do desse `service` rode o comando `node prepare.js`

```bash
$ docker-compose run nodejs node prepare.js
```

Pronto! já temos nossa aplicação funcionando, e para isso basta acessar `http://localhost:<port>/`