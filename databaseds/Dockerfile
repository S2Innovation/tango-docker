FROM tango-buildenv as build

RUN apk add --no-cache mariadb-connector-c-dev

RUN git clone --depth=1 -b Database-Release-5.14 https://github.com/tango-controls/TangoDatabase.git

RUN cd TangoDatabase \
 && mkdir build \
 && cd build \
 && cmake .. -DCMAKE_BUILD_TYPE=Release \
 && make -j4

FROM tango-libtango

RUN apk add --no-cache mariadb-connector-c-dev

COPY --from=build /TangoDatabase/build/Databaseds /

ENTRYPOINT ["/Databaseds"]

CMD ["2", "-ORBendPoint", "giop:tcp::10000"]
