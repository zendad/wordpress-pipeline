FROM ubuntu:latest
MAINTAINER Dereck Zenda<dereck.zenda@gmail.com>

# data will be written in /var/redis/data/

RUN \
  apt-get update && \
  apt-get install -y wget build-essential jq
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install Redis.
RUN \
  cd /tmp && \
  wget http://download.redis.io/redis-stable.tar.gz && \
  tar xf redis-stable.tar.gz && \
  cd redis-stable && \
  make && \
  make install && \
  cp -f src/redis-sentinel /usr/local/bin && \
  mkdir -p /etc/redis && \
  cp -f *.conf /etc/redis && \
  rm -rf /tmp/redis-stable* && \
  echo "vm.overcommit_memory = 1" >> /etc/sysctl.conf

RUN useradd -s /bin/false --create-home --user-group redis

# Define mountable directories.
VOLUME ["/var/redis/data","/etc/redis"]


RUN chown -R redis:redis /var/redis

# Define working directory.
WORKDIR /var/redis/data

# Add scripts
ADD scripts /scripts
ADD scripts/sanity-test.sh /usr/bin/sanity-test
RUN chmod +x /scripts/*.sh /usr/bin/sanity-test
RUN touch /.firstrun

EXPOSE 6379

# Command to run
ENTRYPOINT ["/scripts/run.sh"]
CMD [""]