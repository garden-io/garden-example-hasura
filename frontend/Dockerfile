FROM node:10-alpine

ENV PORT=8080
EXPOSE ${PORT}
WORKDIR /app

RUN npm install -g gatsby-cli

COPY package*.json /app/
RUN npm install

COPY . /app

CMD ["npm", "run", "develop"]
