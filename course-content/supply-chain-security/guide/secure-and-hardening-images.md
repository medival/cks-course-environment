#### Secure and Hardening Images

Tips

1. Use specific package versions
2. Don't run as root
3. Make filesystem read only
4. Remove shell access

Base Image
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

<!-- Implement 1. Use specific package versions -->

`vi Dockerfile`

```
FROM ubuntu
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y golang-go
COPY app.go .
RUN CGO_ENABLED=0 go build app.go

FROM alpine:*3.12.1*
COPY --from=0 /app /home/appuser/
CMD ["./app"]
```

`docker build -t app .`

<!-- Implement 2. Don't run as root -->

`vi Dockerfile`

```
FROM ubuntu
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y golang-go
COPY app.go .
RUN CGO_ENABLED=0 go build app.go

FROM alpine:3.12.1
*RUN addgroup -S appgroup && adduser -S appuser -G appgroup -h /home/appuser*
*COPY --from=0 /app /home/appuser/*
*USER appuser*
*CMD ["/home/appuser/app"]*
```

`docker build -t app .`

<!-- Verify -->

`docker run app`

<!-- uid appuser -->

<!-- Implement 3. Make file system read-only -->

`vi Dockerfile`

```
FROM ubuntu
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y golang-go
COPY app.go .
RUN CGO_ENABLED=0 go build app.go

FROM alpine:3.12.1
*RUN chmod a-w /etc*
RUN addgroup -S appgroup && adduser -S appuser -G appgroup -h /home/appuser
COPY --from=0 /app /home/appuser/
USER appuser
CMD ["/home/appuser/app"]
```

`docker build -t app .`

<!-- Verify -->

`docker run -d app`
`docker exec -it containerid sh`
#/`ls -lh /etc`

<!-- There is only read-only for non-root users in directory /etc -->

<!-- Implement 4. Remove shell access -->

`vi Dockerfile`

```
FROM ubuntu
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y golang-go
COPY app.go .
RUN CGO_ENABLED=0 go build app.go

FROM alpine:3.12.1
RUN chmod a-w /etc
RUN addgroup -S appgroup && adduser -S appuser -G appgroup -h /home/appuser
*RUN rm -rf /bin/**
COPY --from=0 /app /home/appuser/
USER appuser
CMD ["/home/appuser/app"]
```

`docker build -t app .`

<!-- Verify -->

`docker run -d app`
`docker exec -it containerid sh`

<!-- Can't execute container, There is no executable shell -->
