FROM 61.145.163.124:8084/library/nginx:1.27.0-alpine

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8 JAVA_OPTS=""

RUN mkdir -p /data/logs

WORKDIR /app/web

ADD ../dist/ /app/web

COPY ./docker/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf

ADD ./docker/startup.sh /app/startup.sh

RUN chmod +x /app/startup.sh

EXPOSE 80

CMD ["/app/startup.sh"]
