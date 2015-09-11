FROM ubuntu:15.04
MAINTAINER Matthew Hook <hookenz@gmail.com>

# Setup environment
RUN apt-get -y update && apt-get -y install gcc-4.8 \
    wget git make dpkg-dev module-init-tools && \
    mkdir -p /usr/src/kernels && \
    apt-get autoremove && apt-get clean

# Ensure we're using gcc 4.8
RUN update-alternatives --install  /usr/bin/gcc gcc /usr/bin/gcc-4.8 10


# Download kernel source and prepare modules
WORKDIR /usr/src/kernels
RUN git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git linux
WORKDIR linux
RUN git pull
RUN git checkout linux-4.1.y
ADD config /usr/src/kernels/linux/arch/x86/configs/x86_64_defconfig
# RUN zcat /proc/config.gz > .config
RUN make oldconfig
RUN make modules_prepare
# RUN sed -i -e "s/`uname -r`+/`uname -r`/" include/generated/utsrelease.h # In case a '+' was added
# RUN make

CMD ["/bin/bash"]
