FROM nginx
RUN usermod -u 1001 nginx && groupmod -g 1001 nginx
COPY ./.nginx/nginx.conf /etc/nginx/conf.d/default.conf
# RUN mkdir /app && RUN chmod -R 755 /app
WORKDIR /app
COPY ./app* ./
RUN chown -R 1001:1001 /app && chmod -R 755 /app && \
        chown -R 1001:1001 /var/cache/nginx && \
        chown -R 1001:1001 /var/log/nginx && \
        chown -R 1001:1001 /etc/nginx/conf.d
RUN touch /var/run/nginx.pid && \
        chown -R 1001:1001 /var/run/nginx.pid
USER 1001
EXPOSE 8000