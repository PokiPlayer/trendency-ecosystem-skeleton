ARG node_version=14

FROM node:${node_version}

ARG current_service=''
ARG project_root=../../ecosystem/${current_service}/client
ARG app_env=dev
ARG env=local
ARG uid=1000
ARG gid=1000
ARG uname=appuser
ARG npm_token=''

ENV CURRENT_SERVICE ${current_service}
ENV APP_HOME /app
ENV APP_ENV ${app_env}
ENV ENV ${env}
ENV NPM_TOKEN ${npm_token}
ENV APP_HOME /app
WORKDIR ${APP_HOME}

#RUN npm install -g cross-env \
#    && npm rebuild node-sass \
#    && yarn build -q

EXPOSE 4200

CMD ["yarn", "start"]
