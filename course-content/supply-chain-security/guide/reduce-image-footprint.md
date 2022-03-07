#### Reduce Image Footprint with Multi-Stage Build

`vi Dockerfile`

```
FROM ubuntu
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y golang-go
COPY app.go .
RUN CGO_ENABLED=0 go build app.go
```

`docker build -t app .`

`docker image ls | grep app`

<!-- Add new multi-stage build -->

`vi Dockerfile`

```
FROM ubuntu
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y golang-go
COPY app.go .
RUN CGO_ENABLED=0 go build app.go

FROM alpine
COPY --from=0 /app .
CMD ["./app"]
```

`docker build -t app .`
`docker image ls | grep app`

<!-- Size image reduce from 700mb to 7mb -->
