FROM debian:buster-slim as builder

WORKDIR /build

#libsrtp-dev
RUN apt-get update && apt-get -y --no-install-recommends install libmicrohttpd-dev libjansson-dev \
	libssl-dev  libsofia-sip-ua-dev libglib2.0-dev \
	libopus-dev libogg-dev libcurl4-openssl-dev liblua5.3-dev \
	libconfig-dev pkg-config gengetopt libtool libtool-bin automake \
    gtk-doc-tools make build-essential wget cmake golang git ca-certificates libnanomsg-dev


RUN apt-get install -y --no-install-recommends gettext sensible-utils
RUN git clone http://checkinstall.izto.org/checkinstall.git && \
    cd checkinstall && \
    make && \
    make install 


ENV CFLAGS="-O3 -fPIC"
ENV MAKEOPTS="-j4"
ENV CXXFLAGS=$CFLAGS

ENV PKG_CONFIG_PATH=/usr/lib64/pkgconfig/

# LIBNICE
RUN git clone --depth=1 https://gitlab.freedesktop.org/libnice/libnice && \
    cd libnice && \
    ./autogen.sh && \
    ./configure --enable-static-plugins --prefix=/usr  && \
    make && \
    checkinstall --default -D --fstrans=no && \
    #make install && \
    cp libnice*.deb /libnice.deb && \
    cd .. 
    #&& \
    #rm -rf libnice

# RUN find /usr | grep nice
# RUN ls /usr/lib/pkgconfig
# RUN pkg-config --list-all | grep nice
# RUN exit(1)

# LIBSRTP
RUN wget https://github.com/cisco/libsrtp/archive/v2.1.0.tar.gz && \
    tar xfv v2.1.0.tar.gz && \
    cd libsrtp-2.1.0 && \
    ./configure --prefix=/usr/local --enable-openssl && \
    make shared_library && \
    checkinstall --default -D --fstrans=no && \
    #make install && \
    cp libsrtp*.deb /libsrtp.deb && \
    cd .. 
    #&& \
    #rm -rf libsrtp-2.1.0





# BoringSSL
COPY boringlist ./
RUN git clone --depth=1 https://boringssl.googlesource.com/boringssl && \
    cd boringssl && \
    sed -i s/" -Werror"//g CMakeLists.txt && \
    mkdir -p build && \
    cd build && \
    cmake -DCMAKE_CXX_FLAGS="-lrt" -DCMAKE_BUILD_TYPE=Release  .. && \
    make  && \
    #checkinstall --default -D && \
    cd .. && \
    mkdir -p /opt/boringssl && \
    cp -R include /opt/boringssl/ && \
    mkdir -p /opt/boringssl/lib && \
    cp build/ssl/libssl.a /opt/boringssl/lib/ && \
    cp build/crypto/libcrypto.a /opt/boringssl/lib/ && \
    checkinstall   -D --include=../boringlist --default ls && \
    cp boringssl*.deb /boringssl.deb && \
    cd .. 
    #&& \
    #rm -rf boringssl

#usrsctp
#TODO: make static
RUN git clone --depth=1 https://github.com/sctplab/usrsctp && \
    cd usrsctp && \
    ./bootstrap && \
    ./configure --prefix=/usr && \
    make  && \
    checkinstall --default -D --fstrans=no && \
    #make install && \
    cp usrsctp*.deb /usrsctp.deb && \
    cd .. 
    #&& \
    #rm -rf usrsctp


#libwebsockets
RUN git clone --depth=1 https://libwebsockets.org/repo/libwebsockets && \
    cd libwebsockets && \
    mkdir build && \
    cd build && \
    cmake -DLWS_MAX_SMP=1 -DLWS_STATIC_PIC=ON  -DCMAKE_INSTALL_PREFIX:PATH=/usr .. && \
    make && \
    checkinstall --pkgname=libwebsockets --default -D --fstrans=no && \
    #make install && \
    cp libwebsockets*.deb /libwebsockets.deb && \
    cd .. && \
    cd .. 
    #&& \
    #rm -rf libwebsockets


#Paho MQTT
RUN git clone --depth=1 https://github.com/eclipse/paho.mqtt.c.git && \
    cd paho.mqtt.c && \
    mkdir build && \
    cd build && \
    cmake -DPAHO_BUILD_STATIC=TRUE  -DPAHO_WITH_SSL=TRUE -DPAHO_BUILD_DOCUMENTATION=FALSE -DPAHO_BUILD_SAMPLES=FALSE .. && \
    make   &&  \
    checkinstall --pkgname=paho --default -D --fstrans=no && \
    #make install && \
    cp paho*.deb /paho.mqtt.c.deb && \
    cd .. && \
    cd .. 
    #&& \
    #rm -rf paho.mqtt.c

#RabbitMQ
RUN git clone --depth=1 https://github.com/alanxz/rabbitmq-c && \
    cd rabbitmq-c && \
    git submodule init && \
    git submodule update && \
    mkdir build && cd build && \
    cmake -DCMAKE_INSTALL_PREFIX=/usr -DBUILD_SHARED_LIBS=OFF -DBUILD_STATIC_LIBS=ON .. && \
    make  && \
    checkinstall --pkgname=rabbitmq --default -D --fstrans=no && \
    #make install && \
    cp rabbitmq*.deb /rabbitmq-c.deb && \
    cd ..  && \
    cd .. 
    #&& \
    #rm -rf rabbitmq-c


# Build Janus
#aptitude install doxygen graphviz
#--disable-websockets --disable-data-channels --enable-post-processing

RUN git clone --depth=1 https://github.com/meetecho/janus-gateway.git  janusgateway && \
    cd janusgateway && \
    ./autogen.sh && \
    ./configure --prefix=/opt/janus --enable-boringssl --enable-dtls-settimeout && \
    make  && \
    checkinstall --default -D --fstrans=no && \
    cp janusgateway*.deb /janusgateway.deb && \
    #make install && \
    cd .. 
    #&& \
    #rm -rf janus-gateway

FROM debian:buster-slim
WORKDIR /build

RUN apt-get update && apt-get -y --no-install-recommends install libogg-dev libnanomsg-dev libopus-dev libmicrohttpd-dev libsofia-sip-ua-dev libcurl4-openssl-dev libconfig-dev libjansson-dev && \
    apt-get -y clean && \
    rm -rf /usr/share/locale/*  && \
    rm -rf /var/cache/debconf/*-old && \
    rm -rf /usr/share/doc/* && \
    rm -rf /var/lib/apt/*

COPY --from=builder /janusgateway.deb /
COPY --from=builder /libnice.deb /
COPY --from=builder /libsrtp.deb /
COPY --from=builder /boringssl.deb /
COPY --from=builder /usrsctp.deb /
COPY --from=builder /libwebsockets.deb /
COPY --from=builder /paho.mqtt.c.deb /
COPY --from=builder /rabbitmq-c.deb /

RUN dpkg -i /libnice.deb
RUN dpkg -i /libsrtp.deb
RUN dpkg -i /boringssl.deb
RUN dpkg -i /usrsctp.deb
RUN dpkg -i /libwebsockets.deb
RUN dpkg -i /paho.mqtt.c.deb
RUN dpkg -i /rabbitmq-c.deb

RUN dpkg -i /janusgateway.deb

ENV LD_LIBRARY_PATH=/usr/local/lib

CMD /opt/janus/bin/janus --nat-1-1=${DOCKER_IP}
