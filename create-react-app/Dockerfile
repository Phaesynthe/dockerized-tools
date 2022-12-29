FROM node:19-alpine
LABEL maintainer="Matthew Jackson <matthew@phaesynthe.com>"

WORKDIR /output

RUN npm i -g react-app create-react-app

ARG APP_NAME=example-app
ENV APP_NAME="${APP_NAME}"

CMD npm init react-app $APP_NAME
