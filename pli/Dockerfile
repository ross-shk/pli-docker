# Dockerfile for Iron Spring PL/I on Debian 12 (32-bit)
#
# Usage:
#   docker build --platform linux/386 -t pli-dev .
#   docker run --platform linux/386 --security-opt seccomp=unconfined -it pli-dev
# 
# Test:
#   plic -V
#   echo ' main: proc options(main); display("Hello, world!"); end;' > test.pli
#   plic -C -dELF test.pli 
#   gcc -m32 -no-pie -z muldefs -o test test.o -lprf
#   ./test
#
# Note:
#   plic is a 32-bit binary and fails with "SIGACTION 1 returned -1"
#   when run on a 64-bit kernel under Docker. Use linux/386 platform.
#

FROM debian:bullseye-slim

ARG PLI_VERSION=1.4.1

LABEL description="Iron Spring PL/I development environment" \
      version="1.0" \
      pli-version=${PLI_VERSION}

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

WORKDIR /pli-dev

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    file \
    gcc \
    gcc-multilib \
    libc6 \
    libc6-dev \
    make \
    nano \
    pkg-config \
    sudo \
    wget \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Download and install Iron Spring PL/I
RUN wget -q "http://www.iron-spring.com/pli-${PLI_VERSION}.tgz" -O /tmp/pli.tgz \
    && tar -xzf /tmp/pli.tgz -C /tmp \
    && make -C "/tmp/pli-${PLI_VERSION}" install \
    && mkdir -p /usr/lib/pli \
    && cp -r "/tmp/pli-${PLI_VERSION}/lib/"* /usr/lib/pli/ \
    && rm -rf /tmp/pli*

# Download and install plicc (PL/I compile & link toolchain)
RUN wget -q "https://github.com/ross-shk/plic-chain/archive/refs/tags/v1.0.0.tar.gz" -O /tmp/plicc.tgz \
    && tar -xzf /tmp/plicc.tgz -C /tmp \
    && cp /tmp/plic-chain-1.0.0/plicc /usr/local/bin/plicc \
    && chmod 755 /usr/local/bin/plicc \
    && rm -rf /tmp/plicc*

# Create a non-root user for daily work
RUN groupadd -r plidev && useradd -r -g plidev -d /pli-dev -s /bin/bash plidev \
    && echo "plidev ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
    && chown plidev:plidev /pli-dev

USER plidev

ENV PATH="/usr/local/bin:${PATH}" \
    LD_LIBRARY_PATH="/usr/lib/pli:${LD_LIBRARY_PATH}"

CMD ["/bin/bash"]
