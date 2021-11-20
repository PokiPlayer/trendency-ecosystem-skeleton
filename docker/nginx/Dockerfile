ARG openresty_version=1.15.8.1-alpine

FROM openresty/openresty:${openresty_version}

ARG current_service=''
ARG project_root=.

ENV APP_HOME /app
ENV PUBLIC_ROOT ${APP_HOME}/public
ENV CURRENT_SERVICE ${current_service}

WORKDIR ${APP_HOME}

CMD ["/usr/local/openresty/bin/openresty", "-g", "daemon off;"]
