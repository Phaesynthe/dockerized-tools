FROM node:18-alpine
LABEL maintainer="Matthew Jackson <matthew@phaesynthe.com>"

WORKDIR /app

COPY ./package.json package.json
COPY ./package-lock.json package-lock.json

RUN npm i

ARG SPEC_FILE_NAME=main.yml
ENV SPEC_FILE_NAME="${SPEC_FILE_NAME}"

ARG SPEC_SERVICE_NAME="EXAMPLE SERVICE NAME"
ENV SPEC_SERVICE_NAME="${SPEC_SERVICE_NAME}"

CMD ["npm", "start"]