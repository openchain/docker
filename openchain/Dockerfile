FROM ubuntu:14.04

ENV DNX_VERSION 1.0.0-beta7
ENV DNX_USER_HOME /opt/dnx
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get install -y \
    autoconf \
	automake \
    build-essential \
    curl \
    libcurl3-dev \
    libsqlite3-dev \
    libssl-dev \
    libtool \
    libunwind8 \
	unzip
    
RUN curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_USER_HOME=$DNX_USER_HOME DNX_BRANCH=v$DNX_VERSION sh
RUN bash -c "source $DNX_USER_HOME/dnvm/dnvm.sh \
	&& dnvm install $DNX_VERSION -r coreclr -a default \
	&& dnvm alias default | xargs -i ln -s $DNX_USER_HOME/runtimes/{} $DNX_USER_HOME/runtimes/default"

RUN LIBUV_VERSION=1.4.2 \
	&& curl -sSL https://github.com/libuv/libuv/archive/v${LIBUV_VERSION}.tar.gz | tar zxfv - -C /usr/local/src \
	&& cd /usr/local/src/libuv-$LIBUV_VERSION \
	&& sh autogen.sh && ./configure && make && make install \
	&& rm -rf /usr/local/src/libuv-$LIBUV_VERSION \
	&& ldconfig

ENV PATH $PATH:$DNX_USER_HOME/runtimes/default/bin

COPY publish /openchain.server
COPY config.json /openchain.server/wwwroot/App_Data/
COPY NuGet.Config /root/.config/NuGet/

WORKDIR /openchain.server/approot
RUN ["dnu", "restore", "--lock"]

WORKDIR /openchain.server/approot/src/OpenChain.Server
EXPOSE 8080
ENTRYPOINT dnx kestrel --server.urls http://localhost:8080
