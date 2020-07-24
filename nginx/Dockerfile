FROM nginx

MAINTAINER Samuel MARLHENS <samuel.marlhens@gmail.com>

ARG PROJECT_NAME

COPY nginx.conf /etc/nginx/nginx.conf
COPY symfony.conf /etc/nginx/conf.d/default.conf

RUN sed -i s/"symfony.local"/"${PROJECT_NAME}.local"/g /etc/nginx/conf.d/default.conf

RUN echo "upstream php-upstream { server php:9000; }" > /etc/nginx/conf.d/upstream.conf

RUN usermod -u 1000 www-data

CMD ["nginx"]

EXPOSE 80
EXPOSE 443
