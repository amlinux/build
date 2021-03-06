# Copyright 2017 The Go Authors. All rights reserved.
# Use of this source code is governed by a BSD-style
# license that can be found in the LICENSE file.

FROM golang:1.12 AS builder
LABEL maintainer "golang-dev@googlegroups.com"

ENV GO111MODULE=on
ENV GOPROXY=https://proxy.golang.org

RUN mkdir /gocache
ENV GOCACHE /gocache

COPY go.mod /go/src/golang.org/x/build/go.mod
COPY go.sum /go/src/golang.org/x/build/go.sum

WORKDIR /go/src/golang.org/x/build

# Optimization for iterative docker build speed, not necessary for correctness:
# TODO: write a tool to make writing Go module-friendly Dockerfiles easier.
RUN go install cloud.google.com/go/storage
RUN go install golang.org/x/net/http2
RUN go install golang.org/x/crypto/acme/autocert
COPY cmd/pubsubhelper/pubsubtypes /go/src/golang.org/x/build/cmd/pubsubhelper/pubsubtypes
RUN go install golang.org/x/build/cmd/pubsubhelper/pubsubtypes
COPY autocertcache /go/src/golang.org/x/build/autocertcache
RUN go install golang.org/x/build/autocertcache
COPY maintner /go/src/golang.org/x/build/maintner
RUN go install golang.org/x/build/maintner/godata

COPY . /go/src/golang.org/x/build/

# Install binary to /go/bin:
RUN go install golang.org/x/build/devapp

COPY . /go/src/golang.org/x/build/

RUN go install golang.org/x/build/devapp

FROM debian:stretch
COPY --from=builder /go/bin/devapp /
COPY devapp/static /static
COPY devapp/templates /templates
ENTRYPOINT ["/devapp"]
