# Base image: PostgreSQL 16 on Bullseye (amd64/arm64)
FROM postgres:16-bullseye

LABEL maintainer="PostGIS Project - https://postgis.net" \
      org.opencontainers.image.description="PostGIS 3.4.3 spatial extension with PostgreSQL 16, built from source" \
      org.opencontainers.image.source="https://github.com/postgis/docker-postgis"

ENV POSTGIS_MAJOR=3
ENV POSTGIS_VERSION=3.4.3
ENV PG_MAJOR=16

# Install build dependencies for PostGIS
RUN apt-get update \
      && apt-get install -y --no-install-recommends \
      build-essential \
      ca-certificates \
      wget \
      curl \
      gnupg \
      software-properties-common \
      autoconf \
      automake \
      libtool \
      pkg-config \
      libxml2-dev \
      libgeos-dev \
      libproj-dev \
      libgdal-dev \
      libjson-c-dev \
      libprotobuf-c-dev \
      libsqlite3-dev \
      libcurl4-openssl-dev \
      libreadline-dev \
      liblzma-dev \
      libpcre2-dev \
      bison \
      flex \
      postgresql-server-dev-$PG_MAJOR \
      && rm -rf /var/lib/apt/lists/*

# Download and build PostGIS 3.4.3 from source
WORKDIR /usr/src
RUN wget -qO postgis.tar.gz "https://download.osgeo.org/postgis/source/postgis-$POSTGIS_VERSION.tar.gz" \
      && tar xzf postgis.tar.gz \
      && rm postgis.tar.gz \
      && cd postgis-$POSTGIS_VERSION \
      && ./configure --with-pgconfig=/usr/lib/postgresql/$PG_MAJOR/bin/pg_config --without-protobuf \
      && make \
      && make install

# Clean up source directory
RUN rm -rf /usr/src/postgis-$POSTGIS_VERSION

# Init scripts directory
RUN mkdir -p /docker-entrypoint-initdb.d
COPY ./initdb-postgis.sh /docker-entrypoint-initdb.d/10_postgis.sh
COPY ./update-postgis.sh /usr/local/bin

