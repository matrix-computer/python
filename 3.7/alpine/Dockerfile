FROM python:alpine3.7

## STEP 1:
## COMPILE FREETDS WITH OPENSSL, ADD LXML DEV
RUN apk --no-cache add build-base openssl-dev libxml2-dev libxslt-dev openssl patchelf automake autoconf libtool linux-headers m4 gettext-dev git

# get FreeTDS (latest 1.1 branch)
WORKDIR /tmp
RUN git clone https://github.com/FreeTDS/freetds.git 
WORKDIR /tmp/freetds
RUN git checkout Branch-1_1

RUN export CFLAGS="-fPIC" && \
    /bin/sh ./autogen.sh --enable-msdblib \
   --prefix=/usr --sysconfdir=/etc/freetds --with-tdsver=7.4 \
   --disable-apps \
   --disable-server --disable-pool --disable-odbc \ 
   --with-openssl=yes --with-gnutls=no

RUN make && make install

## STEP 2:
## COMPILE PYMSSQL+LXML AND CREATE WHEELS
RUN pip3 install --upgrade pip && pip3 install Cython
RUN pip3 wheel --wheel-dir=/tmp/wheel pymssql==2.1.4
RUN pip3 wheel --wheel-dir=/tmp/wheel lxml
RUN pip3 wheel --wheel-dir=/tmp/wheel pycryptodome


FROM python:alpine3.7

## STEP 1:
## Copy external libraries & wheel from previous stage
COPY --from=0 /tmp/wheel /tmp/wheel
COPY --from=0 /usr/lib/libsybdb.so.5 /usr/lib/libsybdb.so.5 

RUN apk --no-cache add openssl libxml2 libxslt

## install wheel
RUN pip3 install --upgrade pip && pip install --no-index --find-links=/tmp/wheel pymssql lxml pycryptodome

