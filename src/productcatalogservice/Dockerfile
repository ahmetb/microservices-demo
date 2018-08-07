FROM gcr.io/microservices-demo-app/grpc-health-probe:1.0 AS probe

FROM golang:1.10-alpine AS builder
RUN apk add --no-cache ca-certificates git && \
      wget -qO/go/bin/dep https://github.com/golang/dep/releases/download/v0.5.0/dep-linux-amd64 && \
      chmod +x /go/bin/dep

ENV PROJECT github.com/GoogleCloudPlatform/microservices-demo/src/productcatalogservice
WORKDIR /go/src/$PROJECT

# restore dependencies
COPY Gopkg.* ./
RUN dep ensure --vendor-only -v

COPY . .
RUN go build -o /productcatalogservice .

FROM alpine AS release
RUN apk add --no-cache ca-certificates
WORKDIR /productcatalogservice
COPY --from=builder /productcatalogservice ./server
COPY --from=probe   /bin/grpc_health_probe /bin/grpc_health_probe
COPY products.json .
EXPOSE 3550
ENTRYPOINT ["/productcatalogservice/server"]

