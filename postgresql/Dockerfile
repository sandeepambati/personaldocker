FROM debian:8
MAINTAINER Jasper Solutions Inc.

# Add the PostgreSQL PGP key to verify their Debian packages.
# It should be the same key as https://www.postgresql.org/media/keys/ACCC4CF8.asc

RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8

# Add PostgreSQL's repository. It contains the most recent stable release
#     of PostgreSQL, ``9.4``.
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list

# Install ``python-software-properties``, ``software-properties-common`` and PostgreSQL 9.4
#  There are some warnings (in red) that show up during the build. You can hide
#  them by prefixing each apt-get statement with DEBIAN_FRONTEND=noninteractive
RUN apt-get update -qq && DEBIAN_FRONTEND=noninteractive apt-get install -y -q python-software-properties software-properties-common postgresql-9.4 postgresql-client-9.4 postgresql-contrib-9.4  libpq-dev sudo

# Note: The official Debian and Ubuntu images automatically ``apt-get clean``
# after each ``apt-get``

# /etc/ssl/private can't be accessed from within container for some reason
# (@andrewgodwin says it's something AUFS related)
#RUN mkdir /etc/ssl/private-copy; mv /etc/ssl/private/* /etc/ssl/private-copy/; rm -r /etc/ssl/private; mv /etc/ssl/private-copy /etc/ssl/private; chmod -R 0700 /etc/ssl/private; chown -R postgres /etc/ssl/private

ADD postgresql.conf /etc/postgresql/9.4/main/postgresql.conf
ADD pg_hba.conf /etc/postgresql/9.4/main/pg_hba.conf
RUN chown postgres:postgres /etc/postgresql/9.4/main/*.conf
ADD run /usr/local/bin/run
RUN chmod +x /usr/local/bin/run

# Add VOLUMEs to allow backup of config, logs and databases
VOLUME  ["/etc/postgresql/9.4/main", "/var/log/postgresql", "/var/lib/postgresql/9.4/main"]

EXPOSE 5432
CMD ["/usr/local/bin/run"]
