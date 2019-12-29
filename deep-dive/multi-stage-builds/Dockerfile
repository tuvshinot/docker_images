# Create an image for the weather-app using multi-stage build
FROM node AS build
RUN mkdir -p /var/node/
ADD src/ /var/node/
WORKDIR /var/node
RUN npm install

FROM node:alpine
ARG VERSION=V1.1
LABEL org.label-schema.version=$VERSION
ENV NODE_ENV="production"
COPY --from=build /var/node /var/node
WORKDIR /var/node
EXPOSE 3000
ENTRYPOINT ["./bin/www"]
