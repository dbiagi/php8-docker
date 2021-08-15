FROM nginx:1.19.6

RUN mkdir -p /etc/nginx/templates \
    mkdir -p /tmp/nginx

COPY nginx.conf /etc/nginx/
COPY php-upstream.conf /etc/nginx/conf.d/
COPY application.conf /tmp/nginx

ARG NGINX_SERVER_NAME

RUN envsubst '$NGINX_SERVER_NAME' < /tmp/nginx/application.conf > /etc/nginx/conf.d/application.conf; \
    rm -fR /tmp/nginx

RUN cat /etc/nginx/conf.d/application.conf

CMD ["nginx"]

EXPOSE 80
EXPOSE 443