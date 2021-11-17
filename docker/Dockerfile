ARG GOLANG=1.17-buster
FROM golang:${GOLANG} as builder

RUN apt-get update && apt-get install -qy build-essential software-properties-common libjemalloc2 sudo

ENV CGO_ENABLED=0 

#GOOS=linux GOARCH=arm64
RUN go get -u -v google.golang.org/grpc && \
    git clone https://www.github.com/dgraph-io/dgraph/ && \
    cd dgraph && \
    make install

RUN mkdir -p /dist/bin && \
    mkdir -p /dist/tmp && \
    mv ${GOPATH}/bin/dgraph /dist/bin/dgraph

FROM ubuntu:20.04 as dgraph
LABEL maintainer="Dgraph Labs <contact@dgraph.io>"

RUN apt-get update && \
  apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    htop \
    iputils-ping \
    jq \
    less \
    sysstat && \
  curl -fsSL https://deb.nodesource.com/setup_14.x | bash - && \
  apt-get install -y nodejs && \
  rm -rf /var/lib/apt/lists/*

COPY --from=builder /dist /dgraph
ENV PATH=$PATH:/dgraph/bin/
RUN chmod +x /dgraph/bin/dgraph 

EXPOSE 8080
EXPOSE 9080
EXPOSE 5080 
EXPOSE 6080 
EXPOSE 7080 
EXPOSE 8000

WORKDIR /dgraph

ENV GODEBUG=madvdontneed=1
CMD ["/dgraph/bin/dgraph"] # Shows the dgraph version and commands available.
