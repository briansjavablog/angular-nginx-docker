# Stage 1, build app and generate static assets
FROM node:latest as build

#create build directory
WORKDIR /app

# Copy over package*.json so that we can install dependencies before we copy across source code.
# this allows us to cache the node_modules dependencies layer to speed up future builds.
# When the app source code is updated, only the app source is rebuild and we don't pull
# all the dependencies again. The depenencies layer will only be updated when package*.json is updated
COPY package*.json /app/

# install dependencies
RUN npm install

# copy over app source
COPY ./ /app/

# default configuration is production but this can be overridden with a build param
ARG configuration=production

# build app static assets and output to /dist/out
RUN npm run build -- --output-path=./dist/out --configuration $configuration

# Stage 2 uses nginx base image and copies in static assets from build stage above
FROM nginx:latest

# copy static assets to nginx
COPY --from=build /app/dist/out/ /usr/share/nginx/html

# Copy over the custom nginx config file
COPY --from=build /app/nginx-custom.conf /etc/nginx/conf.d/default.conf
