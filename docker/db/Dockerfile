FROM ubuntu:latest
MAINTAINER Dereck Zenda<dereck.zenda@gmail.com>

RUN echo "deb http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted universe multiverse" >> /etc/apt/sources.list

RUN echo "mysql-server mysql-server/root_password password root" | debconf-set-selections
RUN echo "mysql-server mysql-server/root_password_again password root" | debconf-set-selections

RUN apt-get update && \
	apt-get -y install mysql-server-5.7 && \
	mkdir -p /var/lib/mysql && \
	mkdir -p /var/run/mysqld && \
	mkdir -p /var/log/mysql && \
	chown -R mysql:mysql /var/lib/mysql && \
	chown -R mysql:mysql /var/run/mysqld && \
	chown -R mysql:mysql /var/log/mysql \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# UTF-8 and bind-address
COPY ./my.cnf /etc/mysql/my.cnf

VOLUME /var/lib/mysql

COPY ./startup.sh /root/startup.sh
RUN chmod +x /root/startup.sh

ENTRYPOINT ["/root/startup.sh"]

EXPOSE 3306
CMD ["/usr/bin/mysqld_safe"]