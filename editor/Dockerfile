#
# Cartodb container
#
FROM ubuntu:18.04
LABEL maintainer="rabbit-cheng<cheng.donghui@gmail.com>"

# Configuring locales
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && apt-get install -y -q apt-utils software-properties-common locales && dpkg-reconfigure locales && \
  locale-gen en_US.UTF-8 && \
  update-locale LANG=en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

ENV CARTODB_VERSION=master

RUN useradd -m -d /home/cartodb -s /bin/bash cartodb && \
  apt-get install -y -q \
  build-essential \
  autoconf \
  automake \
  libtool \
  checkinstall \
  unp \
  zip \
  unzip \
  git-core \
  git \
  curl \
  libgeos-c1v5 \
  libgeos-dev \
  libjson-c-dev \
  python-simplejson \
  proj-bin \
  proj-data \
  libproj-dev \
  gdal-bin \
  libgdal-dev \
  postgresql-client-10 \
  postgresql-contrib-10 \
  postgresql-plpython-10 \
  postgresql-10-plproxy \
  postgresql-10-postgis-2.4 \
  postgresql-10-postgis-scripts \
  postgis \
  liblwgeom-2.4-0 \
  ca-certificates \
  python2.7-dev \
  python-setuptools \
  imagemagick \
  libmapnik-dev \
  mapnik-utils \
  python-mapnik \
  python-argparse \
  python-gdal \
  python-chardet \
  python-all-dev \
  python-docutils \
  openssl \
  libreadline7 \
  zlib1g \
  zlib1g-dev \
  libssl-dev \
  libyaml-dev \
  libsqlite3-dev \
  libxml2-dev \
  libxslt-dev \
  libc6-dev \
  ncurses-dev \
  bison \
  pkg-config \
  libpq5 \
  libpq-dev \
  libcurl4-gnutls-dev \
  libffi-dev \
  libgdbm-dev \
  gnupg \
  libreadline6-dev \
  libcairo2-dev \
  libjpeg8-dev \
  libpango1.0-dev \
  libgif-dev \
  libgmp-dev \
  libicu-dev \
  wget \
  net-tools \
  ruby2.5-dev \
  xz-utils \
  --no-install-recommends && \
  rm -rf /var/lib/apt/lists/*

RUN git config --global user.email you@example.com
RUN git config --global user.name "Your Name"
RUN git config --global url.https://github.com/.insteadOf git@github.com:


# Install NodeJS
# RUN curl https://nodejs.org/dist/v10.15.3/node-v10.15.3-linux-x64.tar.xz |tar -Jxf - --strip-components=1 -C /usr && \
RUN curl https://nodejs.org/dist/v12.16.3/node-v12.16.3-linux-x64.tar.xz |tar -Jxf - --strip-components=1 -C /usr && \
  npm install -g grunt-cli && \
  npm install -g npm@6 && \
  rm -r /tmp/npm-* /root/.npm

# Install CartoDB
RUN git clone -c submodule."private".update=none --recursive https://github.com/CartoDB/cartodb.git && \
  cd cartodb && \
  git checkout $CARTODB_VERSION

RUN cd /cartodb && \
  npm install && \
  npm run carto-node && npm run build:static && \
  rm -r /tmp/npm-* /root/.npm 

# RUN apt-get install -y -q python-pip 
RUN cd /cartodb && \
  perl -pi -e 's/gdal==1\.10\.0/gdal==2.2.2/' python_requirements.txt && \
  CPLUS_INCLUDE_PATH=/usr/include/gdal  \
  C_INCLUDE_PATH=/usr/include/gdal \
  PATH=$PATH:/usr/include/gdal \
  # pip install --no-binary :all: -r python_requirements.txt
  pip install -r python_requirements.txt

RUN cd /cartodb && \
  gem install bundler --version=1.17.3 && gem install compass archive-tar-minitar rack && \
  bundle update thin && \
  /bin/bash -l -c 'bundle install'

RUN cd /cartodb && \
  cp config/grunt_development.json ./config/grunt_true.json && \
  export PATH=$PATH:$PWD/node_modules/grunt-cli/bin && \
  /bin/bash -l -c 'bundle exec grunt'

