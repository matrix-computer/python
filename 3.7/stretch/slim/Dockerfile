FROM python:3.7-slim

## STEP 1:
## COMPILE FREETDS WITH OPENSSL
RUN apt-get update && apt-get install -y build-essential libssl-dev openssl wget

# get FreeTDS (latest stable realease)
RUN wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.1.tar.gz -O - | tar -xz -C /tmp

# get patched config.c
RUN wget https://raw.githubusercontent.com/matrix-computer/freetds/master/src/tds/config.c -O /tmp/freetds-1.1/src/tds/config.c

WORKDIR /tmp/freetds-1.1

RUN export CFLAGS="-fPIC" && \
    ./configure --enable-msdblib \
   --prefix=/usr --sysconfdir=/etc/freetds --with-tdsver=7.4 \
   --disable-apps \
   --disable-server --disable-pool --disable-odbc \ 
   --with-openssl=yes --with-gnutls=no

RUN make && make install

## STEP 2:
## COMPILE PYMSSQL AND CREATE WHEEL
RUN pip3 install Cython
RUN pip3 wheel --wheel-dir=/tmp/wheel pymssql


FROM python:3.7-slim

## STEP 1:
## Copy external libraries & wheel from previous stage
COPY --from=0 /tmp/wheel /tmp/wheel
COPY --from=0 /usr/lib/libsybdb.so.5 /usr/lib/libsybdb.so.5 
COPY --from=0 /usr/lib/libssl.so.1.0.0 /usr/lib/libssl.so.1.0.0 
COPY --from=0 /usr/lib/libcrypto.so.1.0.0 /usr/lib/libcrypto.so.1.0.0 

## install wheel
RUN pip3 install --upgrade pip && pip install --no-index --find-links=/tmp/wheel pymssql

