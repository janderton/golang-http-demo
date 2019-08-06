FROM golang:alpine as builder
LABEL maintainer="James Anderton <james@janderton.com>"
LABEL description="Demo GoLang App"

WORKDIR /app

RUN apk add --no-cache --update build-base gcc abuild binutils binutils-doc gcc-doc git musl-dev
RUN go get github.com/codegangsta/negroni && \
    go get github.com/mattn/go-sqlite3 && \
    go get github.com/yosssi/ace

ADD . .

### go-sqlite3 requires CGO_ENABLED=1 and static linked mode to work in the scratch container
RUN cd src && CGO_ENABLED=1 GOOS=linux go build -a -ldflags '-linkmode external -extldflags "-static"' -o goapp .

# Final Stage
FROM scratch


WORKDIR /app

ADD src/dev.db /app/dev.db
ADD templates /app/templates/

COPY --from=builder /app/src/goapp /app/goapp

ENV PORT 8080
EXPOSE 8080

ENTRYPOINT ["/app/goapp"]

