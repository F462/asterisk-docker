FROM ubuntu:24.04

ARG ASTERISK_URL=https://downloads.asterisk.org/pub/telephony/asterisk/asterisk-22-current.tar.gz

ARG DEPENDENCIES="sqlite3 libxml2 libjansson4 libedit2"
ARG BUILD_DEPENDENCIES="wget build-essential pkg-config libedit-dev uuid-dev libjansson-dev libxml2-dev libsqlite3-dev libssl-dev"

RUN \
    # Update & install dependencies first
    apt-get update \
    && apt-get install -y ${DEPENDENCIES} ${BUILD_DEPENDENCIES} \
    # Download, compile and install asterisk
    && wget ${ASTERISK_URL} -O asterisk.tar.gz \
    && tar -xvzf asterisk.tar.gz -C / \
    && rm asterisk.tar.gz \
    && cd /asterisk* \
    && ./configure \
    && make \
    && make install \
    # basic asterisk configuration
    && cp -R configs/basic-pbx/* /etc/asterisk/ \
    && cp configs/samples/acl.conf.sample /etc/asterisk/acl.conf \
    && cp configs/samples/ccss.conf.sample /etc/asterisk/ccss.conf \
    && cp configs/samples/cel.conf.sample /etc/asterisk/cel.conf \
    && cp configs/samples/features.conf.sample /etc/asterisk/features.conf \
    && cp configs/samples/indications.conf.sample /etc/asterisk/indications.conf \
    && cp configs/samples/manager.conf.sample /etc/asterisk/manager.conf \
    && cp configs/samples/pjproject.conf.sample /etc/asterisk/pjproject.conf \
    && cp configs/samples/pjsip.conf.sample /etc/asterisk/pjsip.conf \
    && cp configs/samples/queuerules.conf.sample /etc/asterisk/queuerules.conf \
    && cp configs/samples/stasis.conf.sample /etc/asterisk/stasis.conf \
    && cp configs/samples/udptl.conf.sample /etc/asterisk/udptl.conf \
    # put environment variables in place
    && sed -i 's/country=us/country=de/g' /etc/asterisk/indications.conf \
    # install extensions
    && apt-get install -y iptables fail2ban \
    # cleanup
    && cd / \
    && rm -rf /asterisk* \
    && apt-get purge -y ${BUILD_DEPENDENCIES} \
    && apt-get remove -y ${BUILD_DEPENDENCIES} \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

COPY conf/asterisk/logger.conf /etc/asterisk/logger.conf
COPY conf/fail2ban/asterisk.conf /etc/fail2ban/filter.d/asterisk.conf
ARG TMP_JAIL_FILE="/tmp/jail_append.conf"
COPY conf/fail2ban/jail.conf ${TMP_JAIL_FILE}

RUN cat ${TMP_JAIL_FILE} >> /etc/fail2ban/jail.conf \
    && sed -i 's,#ignoreip = .*,ignoreip = 127.0.0.1/8 ::1 192.168.2.0/24,g' /etc/fail2ban/jail.conf \
    && rm ${TMP_JAIL_FILE}

CMD [ "asterisk", "-vvvc" ]