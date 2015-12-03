FROM ubuntu:14.04

ENV DNX_VERSION 1.0.0-rc1-final
ENV OPENCHAIN_TAG v0.4.4

ENV DNX_USER_HOME /opt/dnx
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get install -y \
    autoconf \
    automake \
    build-essential \
    curl \
    gettext \
    git \
    icu-devtools \
    libcurl3-gnutls \
    libsqlite3-dev \
    libssl-dev \
    libtool \
    libunwind8 \
    unzip \
    zlib1g
    
RUN curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_USER_HOME=$DNX_USER_HOME DNX_BRANCH=v$DNX_VERSION sh
RUN bash -c "source $DNX_USER_HOME/dnvm/dnvm.sh \
	&& dnvm install $DNX_VERSION -r coreclr -alias default -arch x64 \
	&& dnvm alias default | xargs -i ln -s $DNX_USER_HOME/runtimes/{} $DNX_USER_HOME/runtimes/default"

RUN LIBUV_VERSION=1.4.2 \
	&& curl -sSL https://github.com/libuv/libuv/archive/v${LIBUV_VERSION}.tar.gz | tar zxfv - -C /usr/local/src \
	&& cd /usr/local/src/libuv-$LIBUV_VERSION \
	&& sh autogen.sh && ./configure && make && make install \
	&& rm -rf /usr/local/src/libuv-$LIBUV_VERSION \
	&& ldconfig

ENV PATH $PATH:$DNX_USER_HOME/runtimes/default/bin

RUN cd /tmp \
    && git clone https://github.com/openchain/openchain.git openchain \
    && cd openchain \
    && git checkout $OPENCHAIN_TAG \
    && cd src/Openchain.Server \
    && dnu restore \
    && dnu publish -o "/openchain.server" --iis-command start --configuration release --framework dnxcore50 \
    && rm -rf /tmp/openchain \
    && rm -rf /root/.dnx/packages

WORKDIR /openchain.server/approot/src/Openchain.Server
EXPOSE 8080
ENTRYPOINT dnx start --server.urls http://0.0.0.0:8080
