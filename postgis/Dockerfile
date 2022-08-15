FROM postgres:10.0

RUN apt-get update \
  && apt-get -y --no-install-recommends install \
  build-essential \
  git \
  # PostgreSQL
  libpq-dev \
  libpq5 \
  postgresql-contrib-10 \
  postgresql-plpython-10 \
  postgresql-server-dev-10 \
  # GIS:
  # - Proj
  libproj-dev \
  proj-bin \
  proj-data \
  # - JSON
  libjson-c3 \
  libjson-c-dev \
  python-simplejson \
  # - GEOS
  libgeos-c1v5 \
  libgeos-dev \
  # - GDAL
  libgdal20 \
  gdal-bin \
  libgdal-dev \
  # PostGIS
  liblwgeom-2.4\* \
  libxml2-dev \
  postgis-2.4 \
  postgresql-10-postgis-2.4 \
  postgresql-10-postgis-2.4-scripts \
  && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /docker-entrypoint-initdb.d

COPY initdb-postgis.sh /docker-entrypoint-initdb.d/01-postgis.sh

# Let Postgres listen on all addresses (it's just localhost by default).
ENTRYPOINT [ "docker-entrypoint.sh", "postgres", "-c", "listen_addresses=*" ]

RUN git clone https://github.com/CartoDB/cartodb-postgresql.git
RUN cd cartodb-postgresql
RUN git checkout master 
RUN make all install

# Empty CMD directive to override CMD in the base image.
# Additional configuration options can be passed via a custom command.
CMD []