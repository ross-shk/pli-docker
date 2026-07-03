# Dockerfile for Iron Spring PL/I on Ubuntu 22.04
# Usage:
#   docker build -t pli-dev .
#   docker run -it pli-dev

FROM ubuntu:22.04

ARG PLI_VERSION=1.4.1

LABEL description="Iron Spring PL/I development environment" \
      version="1.0" \
      pli-version=${PLI_VERSION}

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

WORKDIR /pli-dev

# Install dependencies (32-bit support required for PL/I)
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc-multilib \
    libc6-i386 \
    make \
    wget \
    ca-certificates \
    pkg-config \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Download and install Iron Spring PL/I
RUN wget -q "http://www.iron-spring.com/pli-${PLI_VERSION}.tgz" -O /tmp/pli.tgz \
    && tar -xzf /tmp/pli.tgz -C /tmp \
    && make -C "/tmp/pli-${PLI_VERSION}" install \
    && mkdir -p /usr/lib/pli \
    && cp -r "/tmp/pli-${PLI_VERSION}/lib/"* /usr/lib/pli/ \
    && rm -rf /tmp/pli*

# Verify installation
RUN plic -V

# Create a non-root user for daily work
RUN groupadd -r plidev && useradd -r -g plidev -d /pli-dev -s /bin/bash plidev \
    && chown plidev:plidev /pli-dev

USER plidev

CMD ["/bin/bash"]
