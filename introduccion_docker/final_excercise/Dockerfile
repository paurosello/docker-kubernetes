FROM node:8
WORKDIR /usr/src/app
COPY . .
RUN npm install
RUN npm install -g grunt-cli

EXPOSE 8001
CMD [ "grunt", "connect" ]