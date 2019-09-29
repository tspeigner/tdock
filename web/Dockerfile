FROM nginx:alpine
RUN rm /etc/nginx/conf.d/default.conf
## Not sure if the below is still needed, used for basic auth
## Keeping in for now, until further testing, but things are working now
RUN apk add apache2-utils