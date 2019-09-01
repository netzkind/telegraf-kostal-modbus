FROM golang:1.12.7-stretch AS builder

RUN apt-get update \
    && apt-get --no-install-recommends -y install \
        curl \
        git

RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
RUN curl -o /tmp/4623.patch https://patch-diff.githubusercontent.com/raw/influxdata/telegraf/pull/4623.patch
RUN curl -o /tmp/modbus.go https://raw.githubusercontent.com/garciaolais/telegraf/d99586ead407f428dd811dbe909d70fdc26748b4/plugins/inputs/modbus/modbus.go

# rather hideous, but we need 1.8.0 to apply the patch, yet we want to use 1.11.2 for its feature glory
RUN go get -d -v github.com/influxdata/telegraf \
    && go get -d -v github.com/goburrow/modbus \
    && cd $GOPATH/src/github.com/influxdata/telegraf \
    && git checkout 1.8.0 \
    && git apply /tmp/4623.patch \
    && cp /tmp/modbus.go plugins/inputs/modbus/modbus.go \
    && git config user.email "a@a.a" \
    && git config user.name "a" \
    && git stash \
    && git checkout 1.11.2 \
    && git stash apply \
    && make deps static \
    && mkdir -p /build \
    && cp telegraf /build/telegraf

RUN curl -o /build/entrypoint.sh https://raw.githubusercontent.com/influxdata/influxdata-docker/7f19c2e0e461d745be701290469255b9bc3eeef2/telegraf/1.11/entrypoint.sh \
    && chmod ugo+x /build/entrypoint.sh



FROM buildpack-deps:stretch-curl

RUN DEBIAN_FRONTEND=noninteractive apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
            iputils-ping \
            snmp \
            procps \
            lm-sensors  \
    && rm -rf /var/lib/apt/lists/* 

EXPOSE 8125/udp 8092/udp 8094

COPY --from=builder /build/entrypoint.sh /entrypoint.sh
COPY --from=builder /build/telegraf /usr/bin/telegraf
ENTRYPOINT ["/entrypoint.sh"]
CMD ["telegraf"]

# and our very own config-file
COPY telegraf.conf /etc/telegraf/telegraf.conf