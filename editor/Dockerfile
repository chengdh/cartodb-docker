FROM ubuntu:16.04

# Set locale.
RUN apt-get clean && apt-get update && apt-get install -y locales software-properties-common curl
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash - \
  && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
  && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN locale-gen en_US.UTF-8 && update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8 \
  # Install python-software-properties package to make it easier to use PPAs.
  && apt-get update && apt-get -y --no-install-recommends install \
  python-software-properties \
  # Add PPAs.
  # && add-apt-repository ppa:cartodb/gcc \
  && add-apt-repository ppa:cartodb/gis \
  && add-apt-repository ppa:cartodb/nodejs \
  && add-apt-repository ppa:cartodb/postgresql-10 \
  && apt-add-repository ppa:brightbox/ruby-ng \
  # Install apt packages.
  && apt-get update && apt-get -y --no-install-recommends install \
  # build essentials
  autoconf \
  binutils-doc \
  bison \
  build-essential \
  flex \
  make \
  pkg-config \
  gcc-4.9 \
  g++-4.9 \
  # Git
  git \
  # PostgreSQL
  libpq5 \
  libpq-dev \
  postgresql-client-10 \
  postgresql-client-common \
  # GIS:
  # - Proj
  # proj \
  # proj-bin \
  # proj-data \
  # libproj-dev \
  # - JSON
  libjson0 \
  libjson0-dev \
  python-simplejson \
  # - GEOS
  libgeos-c1v5 \
  libgeos-dev \
  # - GDAL
  gdal-bin \
  libgdal-dev \
  # Postgis
  libxml2-dev \
  postgis \
  postgresql-10-postgis-2.4 \
  postgresql-10-postgis-scripts \
  # NodeJS and dependencies
  libcairo2-dev \
  libgif-dev \
  libjpeg-dev \
  libpango1.0-dev \
  libpixman-1-0 \
  libpixman-1-dev \
  nodejs \
  yarn \
  # Ruby dependencies
  libreadline6-dev \
  openssl \
  # Python dependencies
  imagemagick \
  python-all-dev \
  unp \
  zip \
  libicu-dev \
  # Other dependencies
  unzip \
  wget \
  # Install Ruby.
  ruby2.6 \
  ruby2.6-dev \
  # ruby-bundler \
  # Install Pip.
  python-pip

RUN apt-get install -y  python-setuptools

ENV CC=/usr/bin/gcc-4.9
ENV CXX=/usr/bin/g++-4.9
ENV PATH=/opt/rubies/ruby-2.6/bin:$PATH

# Install Ruby utilities.
RUN gem install compass
RUN gem install bundler -v 1.11.2 

# CartoDB/cartodb version
ENV CARTODB_VERSION=4.45.0

# Download the Editor program, using the specified release tag.
# RUN git clone -c http.sslVerify=false --recursive --branch v${CARTODB_VERSION} --depth 1 https://github.com/CartoDB/cartodb.git
# RUN git clone -c http.sslVerify=false --branch v${CARTODB_VERSION} --depth 1 https://github.com/CartoDB/cartodb.git
RUN git clone -c http.sslVerify=false https://github.com/CartoDB/cartodb.git



WORKDIR /cartodb
# Environment type.
ENV RAILS_ENV=development

# Install Ruby dependencies.
RUN bundle install
# Install Node dependencies.
RUN yarn install
# Add dependencies of configuration merge script.
# RUN yarn add \
#   js-yaml \
#   yargs

RUN npm run carto-node && npm run build:static
# RUN PATH=$PATH:/cartodb/node_modules/grunt-cli/bin \
#   bundle exec grunt --environment=${RAILS_ENV}

# Install Python dependencies.
RUN CPLUS_INCLUDE_PATH=/usr/include/gdal \
  C_INCLUDE_PATH=/usr/include/gdal \
  PATH=$PATH:/usr/include/gdal \
  pip install  --no-use-wheel  -r python_requirements.txt \
  # Build the project.
  # && bundle install \
  # Clean up.
  && rm -rf \
  /root/.cache \
  /root/.bundle \
  /root/.gem \
  /root/.npm
  # && mkdir -p /cartodb/docker-entrypoint-util

# Copy resources.
# COPY util/* /cartodb/docker-entrypoint-util/
# COPY config/* /cartodb/config/
# COPY docker-entrypoint.sh /cartodb/


EXPOSE 3000

# We configure the application using just the base config files here. Additional configuration files
# can be merged with the base by supplying their filenames as --app_config or --database option
# arguments in the CMD.
# ENTRYPOINT [ \
#   "./docker-entrypoint.sh", \
#   "--app_config", "base.app_config.yml", \
#   "--database", "base.database.yml" \
#   ]

# We merge the sample config files with the base configs by default.
# Custom configuration may be used by extending this image as follows:
# 1) COPY one or more config files into /cartodb/config/.
# 2) Pass the filename(s) as --app_config or --database option arguments in the CMD.
# CMD [ \
#   "--app_config", "app_config.yml.sample", \
#   "--database", "database.yml.sample" \
#   ]
