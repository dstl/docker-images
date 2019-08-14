FROM ubuntu:14.04

WORKDIR /opt/bin/moses

# Install dependencies
RUN apt-get update && apt-get install -q -y build-essential git-core pkg-config automake libtool wget zlib1g-dev python-dev libbz2-dev cmake python

# Download Moses
RUN git clone --depth 1 https://github.com/moses-smt/mosesdecoder.git && git clone --depth 1 https://github.com/moses-smt/mgiza.git

# Build Moses
WORKDIR /opt/bin/moses/mosesdecoder
RUN make -f contrib/Makefiles/install-dependencies.gmake PREFIX=/opt \
    && OPT=/opt ./compile.sh --prefix=/opt/bin/moses --install-scripts

# Build MGiza
WORKDIR /opt/bin/moses/mgiza/mgizapp
RUN cmake . \
    && make \
    && make install \
    && mkdir -p /opt/bin/tools \
    && cp bin/* scripts/merge_alignment.py /opt/bin/tools

# Tidy up
WORKDIR /opt/bin/moses
RUN rm -Rf mosesdecoder \
    && rm -Rf mgiza \
    && apt-get remove -q -y build-essential git-core pkg-config automake libtool wget zlib1g-dev python-dev libbz2-dev cmake \
    && apt-get autoremove -q -y

ENV LD_LIBRARY_PATH /opt/lib

VOLUME /mnt/model

EXPOSE 8080

ENTRYPOINT /opt/bin/moses/bin/mosesserver -f /mnt/model/moses.ini

