FROM armhf/alpine:3.4

RUN export GRAFANA_VERSION=v3.1.1 \
    && export GOPATH=/go \
    && PATH=$PATH:$GOPATH/bin \
    && apk add --update build-base nodejs go git mercurial \
    && mkdir -p /go/src/github.com/grafana && cd /go/src/github.com/grafana \
    && git clone https://github.com/grafana/grafana.git -b ${GRAFANA_VERSION}
RUN cd /go/src/github.com/grafana/grafana \
    && go run build.go setup \
#&& godep restore \
    && go run build.go build
RUN cd /go/src/github.com/grafana/grafana \
    && npm install \
    && npm install -g grunt-cli
RUN cd /go/src/github.com/grafana/grafana/node_modules/karma-phantomjs-launcher/node_modules/phantomjs && node install 
RUN cd /go/src/github.com/grafana/grafana && grunt
    && npm uninstall -g grunt-cli \
    && npm cache clear \
    && mkdir -p /usr/share/grafana/bin/ \
    && cp -a /go/src/github.com/grafana/grafana/grafana /usr/share/grafana/bin/grafana-server \
    && cp -ra /go/src/github.com/grafana/grafana/public_gen /usr/share/grafana \
    && mv /usr/share/grafana/public_gen /usr/share/grafana/public \
    && cp -ra /go/src/github.com/grafana/grafana/conf /usr/share/grafana \
    && go clean -i -r \
    && apk del --purge build-base nodejs go git mercurial \
    && rm -rf /go /tmp/* /var/cache/apk/* /root/.n* /usr/local/bin/phantomjs

RUN addgroup -g 40561 stoic && adduser -u 40561 -G stoic -D stoic

EXPOSE 3000
WORKDIR /usr/share/grafana/
CMD ["/usr/share/grafana/bin/grafana-server"]
