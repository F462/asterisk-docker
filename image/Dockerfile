FROM ubuntu:24.04

ARG ASTERISK_URL=https://downloads.asterisk.org/pub/telephony/asterisk/asterisk-22-current.tar.gz

ARG DEPENDENCIES="sqlite3 libxml2 libjansson4 libedit2"
ARG BUILD_DEPENDENCIES="wget build-essential pkg-config libedit-dev uuid-dev libjansson-dev libxml2-dev libsqlite3-dev libssl-dev"

RUN apt-get update \
    && apt-get install -y ${DEPENDENCIES} ${BUILD_DEPENDENCIES} \
    && wget ${ASTERISK_URL} -O asterisk.tar.gz \
    && tar -xvzf asterisk.tar.gz -C / \
    && rm asterisk.tar.gz \
    && cd /asterisk* \
    && ./configure \
    && make \
    && make install \
    && apt-get purge -y ${BUILD_DEPENDENCIES} \
    && apt-get remove -y ${BUILD_DEPENDENCIES} \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

CMD [ "asterisk", "-vvvc" ]