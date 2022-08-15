FROM buildpack-deps:stretch

RUN curl -sL https://deb.nodesource.com/setup_12.x | bash - \
  && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
  && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
  && apt-get update \
  && apt-get -y --no-install-recommends install \
  build-essential \
  git \
  libcairo2-dev \
  libgif-dev \
  libjpeg-dev \
  libpango1.0-dev \
  libpixman-1-0 \
  libpixman-1-dev \
  nodejs \
  yarn \
  && rm -rf /var/lib/apt/lists/*

#
# CartoDB/CartoDB-SQL-API version
ENV CARTODB_SQL_API_VERSION=master

# RUN mkdir /CartoDB-SQL-API \
#   && curl -L https://github.com/CartoDB/CartoDB-SQL-API/archive/${CARTODB_SQL_API_VERSION}.tar.gz \
#   | tar -zx --strip-components=1 -C CartoDB-SQL-API \
RUN  git clone https://github.com/CartoDB/CartoDB-SQL-API.git \
  && cd CartoDB-SQL-API \
  && git checkout $CARTODB_SQL_API_VERSION \
  && npm install

# Copy resources.
COPY config/*.js /CartoDB-SQL-API/config/environments/
COPY docker-entrypoint.sh /usr/local/bin/

WORKDIR /CartoDB-SQL-API

# We configure the application using just the base config here. Additional configuration files can
# be merged with the base by supplying their filenames as the CMD.
ENTRYPOINT [ "docker-entrypoint.sh", "base.config.js" ]

# We use the development example config by default. Alternatives:
# - production.js.example
# - staging.js.example
# - test.js.example
# OR extend this image by COPYing a custom config file into /CartoDB-SQL-API/config/environments/
# and passing its name as the CMD.
CMD [ "development.js.example" ]
