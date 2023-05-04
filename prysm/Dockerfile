# Adapted from https://github.com/avalonche/eth-pos-devnet/blob/master/consensus/Dockerfile

FROM golang:bullseye as bazelisk
RUN go install github.com/bazelbuild/bazelisk@latest

FROM golang:bullseye as beacon-chain
COPY --from=bazelisk ${GOPATH}/bin/bazelisk ${GOPATH}/bin/bazelisk
RUN ln -s ${GOPATH}/bin/bazelisk /usr/local/bin/bazel
ENV PATH ${PATH}:${GOPATH}/bin

# This both verifies that bazel is in $PATH,
# and also caches the latest bazel release at the time of docker build.
RUN bazel version

RUN apt-get update && apt-get install -y \
                apt-transport-https \
                autoconf \
                cmake \
                #curl \
                #docker.io \
                gcc \
                gcc-aarch64-linux-gnu \
                g++ \
                g++-aarch64-linux-gnu \
                gnupg2 \
                make \
                mingw-w64 \
                libgmp-dev \
                libssl-dev \
                libtinfo5 \
                libtool \
                libxml2-dev \
                patch \
                pkg-config \
                #python \
                #unzip \
                xz-utils \
                #zip \
                zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

#RUN go install github.com/bazelbuild/bazelisk@latest
#
#RUN set -eux; \
#    cp /go/bin/bazelisk /usr/local/bin/bazelisk; \
#    ln -s /go/bin/bazelisk /usr/local/bin/bazel;
#
#RUN bazel version

WORKDIR /workspace

ARG COMMIT
RUN git clone https://github.com/flashbots/prysm.git . && git checkout ${COMMIT}
RUN bazel build //cmd/beacon-chain:beacon-chain --config=release

ENTRYPOINT ["bazel","run","//cmd/beacon-chain:beacon-chain","--config=release","--"]
