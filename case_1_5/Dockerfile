# Docker Meteor
FROM node:4.8.1-alpine
MAINTAINER Docker Cbba

ENV BUILD_PACKAGES="python make gcc g++ git libuv bash curl tar bzip2" \
    NODE_ENV=production \
    ROOT_URL=http://localhost:3000 \
    PORT=3000

WORKDIR /root/app/bundle

ADD todos.tar.gz /root/app
RUN apk --update add ${BUILD_PACKAGES} \
    && (cd programs/server/ && npm install --unsafe-perm) \
    && apk --update del ${BUILD_PACKAGES}

EXPOSE 3000
CMD node main.js
