FROM alpine:3.10

ARG OMNIORB_VERSION=4.2.3
ARG ZMQ_VERSION=v4.3.2
ARG CPPZMQ_VERSION=v4.5.0
ARG CPPTANGO_VERSION=9.3.3

ARG PREFIX=/tango

ENV PKG_CONFIG_PATH=${PREFIX}/lib64/pkgconfig

RUN mkdir ${PREFIX}

RUN apk add --no-cache git g++ make cmake python-dev

RUN wget https://astuteinternet.dl.sourceforge.net/project/omniorb/omniORB/omniORB-${OMNIORB_VERSION}/omniORB-${OMNIORB_VERSION}.tar.bz2 \
 && tar xf omniORB-*.tar.bz2 \
 && rm omniORB-*.tar.bz2

RUN cd /omniORB-* \
 && ./configure --prefix=${PREFIX} --libdir=${PREFIX}/lib64 --disable-static \
 && make -j4 \
 && make install

COPY libzmq-fix-libdir.patch /
RUN git clone --depth=1 -b ${ZMQ_VERSION} https://github.com/zeromq/libzmq.git \
 && cd /libzmq \
 && git apply ../libzmq-fix-libdir.patch

RUN cd /libzmq \
 && mkdir build \
 && cd build \
 && cmake .. \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DZMQ_BUILD_TESTS=OFF \
    -DBUILD_STATIC=OFF \
    -DENABLE_DRAFTS=OFF \
    -DCMAKE_BUILD_TYPE=Release \
 && make -j4 \
 && make install

COPY cppzmq-fix-unsupported-libzmq.patch /
RUN git clone --depth=1 -b ${CPPZMQ_VERSION} https://github.com/zeromq/cppzmq.git \
 && cd /cppzmq \
 && git apply ../cppzmq-fix-unsupported-libzmq.patch

RUN cd /cppzmq \
 && mkdir build \
 && cd build \
 && cmake .. \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DCPPZMQ_BUILD_TESTS=OFF \
    -DENABLE_DRAFTS=OFF \
    -DCMAKE_BUILD_TYPE=Release \
 && make -j4 \
 && make install

RUN git clone --depth=1 https://github.com/tango-controls/tango-idl

RUN cd /tango-idl \
 && mkdir build \
 && cd build \
 && cmake .. \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
 && make \
 && make install

COPY cpptango-fix-musl-libc-compilation.patch /
RUN git clone --depth=1 -b ${CPPTANGO_VERSION} https://github.com/tango-controls/cppTango.git \
 && cd /cppTango \
 && git apply ../cpptango-fix-musl-libc-compilation.patch

RUN cd /cppTango \
 && mkdir build \
 && cd build \
 && cmake .. \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DCPPZMQ_BASE=${PREFIX} \
    -DOMNIIDL_PATH=${PREFIX}/bin/ \
    -DUSE_PCH=ON \
    -DCMAKE_BUILD_TYPE=Release \
 && make -j4 tango tango-static \
 && make install/fast
