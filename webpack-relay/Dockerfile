MAINTAINER Cole Turner <turner.cole@gmail.com>
FROM node:7.7.2

ENV GRAPHQL_ENDPOINT http://web:80/graphql
ENV SCHEMA_PATH /mnt/app/graphql
ENV SCHEMA_EXT '*.rb'
ENV NODE_ENV development
ENV WATCHMAN_VERSION v4.7.0
ENV PATH="/usr/local/bin:$PATH"

RUN apt-get update && apt-get install -y supervisor

RUN cd /home/node &&\
  git clone https://github.com/facebook/watchman.git &&\
  cd watchman && git checkout $WATCHMAN_VERSION &&\
 ./autogen.sh && ./configure --without-python &&\
  make && make install &&\
  cd ~ && rm -fr watchman && \
	apt-get purge -y --auto-remove $buildDeps

RUN yarn global add webpack-dev-server

COPY ["./package.json", "/mnt/"]
WORKDIR /mnt
RUN yarn install

ADD . /mnt

RUN watchman watch $SCHEMA_PATH
RUN watchman -- trigger $SCHEMA_PATH buildschema $SCHEMA_EXT -- node /mnt/build/generate.js
CMD ["/mnt/node_modules/.bin/webpack-dev-server", "--progress", "--host", "0.0.0.0"]
