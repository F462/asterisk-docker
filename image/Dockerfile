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
    && cp configs/samples/pjproject.conf.sample /etc/asterisk/pjproject.conf \
    && cp configs/samples/pjsip.conf.sample /etc/asterisk/pjsip.conf \
    && cp configs/samples/stasis.conf.sample /etc/asterisk/stasis.conf \
    && cp configs/samples/udptl.conf.sample /etc/asterisk/udptl.conf \
    # put environment variables in place
    && sed -i 's/country=us/country=$COUNTRY/g' /etc/asterisk/indications.conf \
    # cleanup
    && cd / \
    && rm -rf /asterisk* \
    && apt-get purge -y ${BUILD_DEPENDENCIES} \
    && apt-get remove -y ${BUILD_DEPENDENCIES} \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

CMD [ "asterisk", "-vvvc" ]