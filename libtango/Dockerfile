FROM tango-buildenv as build

RUN find /tango/lib64 -type l | xargs rm
RUN find /tango/lib64 -name '*.a' | xargs rm

FROM alpine:3.10

ARG PREFIX=/tango

ENV LD_LIBRARY_PATH=${PREFIX}/lib64
ENV PKG_CONFIG_PATH=${PREFIX}/lib64/pkgconfig

RUN apk add --no-cache libstdc++

COPY --from=build ${PREFIX}/lib64 ${PREFIX}/lib64

RUN ldconfig ${PREFIX}/lib64
