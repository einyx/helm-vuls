FROM golang:alpine as builder

RUN apk add --no-cache \
        git \
        make \
        gcc \
        musl-dev

ENV REPOSITORY github.com/knqyf263/gost
RUN git clone https://github.com/knqyf263/gost $GOPATH/src/$REPOSITORY
RUN cd $GOPATH/src/$REPOSITORY && make install


FROM alpine:3.7

MAINTAINER alessio

ENV LOGDIR /var/log/vuls
ENV WORKDIR /vuls

RUN apk add --no-cache ca-certificates git \
    && mkdir -p $WORKDIR $LOGDIR

COPY --from=builder /go/bin/gost /usr/local/bin/

VOLUME ["$WORKDIR", "$LOGDIR"]
WORKDIR $WORKDIR
ENV PWD $WORKDIR

ENTRYPOINT ["gost"]
CMD ["--help"]
