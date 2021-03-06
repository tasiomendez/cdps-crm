FROM ubuntu:16.04

RUN apt-get update
RUN apt-get install -y glusterfs-server build-essential libgd2-xpm-dev openssl libssl-dev unzip curl

RUN mkdir /nas

EXPOSE 111 24007 24008 2049 38465 38466 38467 49152 49153 49514

# Install NRPE

RUN useradd nagios
WORKDIR /root/
RUN curl -L -O http://nagios-plugins.org/download/nagios-plugins-2.2.1.tar.gz

RUN tar zxf nagios-plugins-2.2.1.tar.gz
WORKDIR nagios-plugins-2.2.1

RUN ./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl

RUN make
RUN make install

WORKDIR /root/
RUN curl -L -O https://github.com/NagiosEnterprises/nrpe/releases/download/nrpe-3.2.1/nrpe-3.2.1.tar.gz
RUN tar zxf nrpe-3.2.1.tar.gz
WORKDIR nrpe-3.2.1

RUN ./configure --enable-command-args --with-nagios-user=nagios --with-nagios-group=nagios --with-ssl=/usr/bin/openssl --with-ssl-lib=/usr/lib/x86_64-linux-gnu --with-init-type=systemd --with-inetd-type=systemd

RUN make all && make install && make install-config && make install-init

RUN sed -i "s+allowed_hosts=127.0.0.1,::1+#allowed_hosts=127.0.0.1,::1+" /usr/local/nagios/etc/nrpe.cfg
RUN sed -i "s+-r -w .15,.10,.05 -c .30,.25,.20+-r -w .85,.75,.65 -c .95,.85,.75+" /usr/local/nagios/etc/nrpe.cfg

EXPOSE 5666

WORKDIR /root/

CMD /usr/local/nagios/bin/nrpe -c /usr/local/nagios/etc/nrpe.cfg -d && service glusterfs-server restart && tail -f /dev/null
