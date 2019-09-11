#
# PX4 NuttX development environment
#

FROM uaventure/px4io-px4-dev-simulation:20180103
MAINTAINER Andreas Antener <andreas@uaventure.com>

RUN dpkg --add-architecture i386 \
	&& apt-get update \
	&& DEBIAN_FRONTEND=noninteractive apt-get -y --quiet --no-install-recommends install \
		autoconf \
		automake \
		bison \
		build-essential \
		bzip2 \
		file \
		flex \
		genromfs \
		gperf \
		libc6:i386 \
		libgcc1:i386 \
		libncurses-dev \
		libtool \
		pkg-config \
		vim-common \
	&& apt-get -y autoremove \
	&& apt-get clean autoclean \
	# gcc-arm-embedded 5-2016-q3-update (https://developer.arm.com/open-source/gnu-toolchain/gnu-rm/downloads)
	&& mkdir -p /opt/gcc && cd /opt/gcc \
	&& wget -qO- "https://developer.arm.com/-/media/Files/downloads/gnu-rm/5_4-2016q3/gcc-arm-none-eabi-5_4-2016q3-20160926-linux.tar.bz2?product=GNU%20ARM%20Embedded%20Toolchain,32-bit,,Linux,5-2016-q3-update" | tar jx --strip 1 \
	&& rm -rf /opt/gcc/share/doc \
	&& rm -rf /var/lib/apt/lists/{apt,dpkg,cache,log} /tmp/* /var/tmp/*

ENV PATH="$PATH:/opt/gcc/bin"

# manual ccache setup for arm-none-eabi-g++/arm-none-eabi-gcc
RUN ln -s /usr/bin/ccache /usr/lib/ccache/arm-none-eabi-g++ \
	&& ln -s /usr/bin/ccache /usr/lib/ccache/arm-none-eabi-gcc

# nuttx tools
RUN git clone --depth 1 https://bitbucket.org/nuttx/tools.git /tmp/tools \
	&& cd /tmp/tools/kconfig-frontends \
	&& autoreconf -f -i \
	&& ./configure --enable-mconf --disable-nconf --disable-gconf --disable-qconf -prefix=/usr && make && make install \
	&& rm -rf /tmp/*

# bloaty - https://github.com/google/bloaty
RUN git clone --recursive https://github.com/google/bloaty.git /tmp/bloaty \
	&& cd /tmp/bloaty && cmake -GNinja . && ninja bloaty && cp bloaty /usr/local/bin/ \
	&& rm -rf /tmp/*

# addition for grpc sim setup
RUN DEBIAN_FRONTEND=noninteractive apt-get -y --quiet --no-install-recommends install \
		libgrpc-dev \
	&& rm -rf /var/lib/apt/lists/{apt,dpkg,cache,log} /tmp/* /var/tmp/*
