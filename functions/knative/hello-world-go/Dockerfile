ARG GOPROXY="https://goproxy.cn,direct"

# Build the openfunction binary
FROM golang:1.19 as builder
ARG GOPROXY

WORKDIR /workspace
# Copy the Go Modules manifests
COPY go.mod go.mod
COPY go.sum go.sum
# cache deps before building and copying source so that we don't need to re-download as much
# and so that source changes don't invalidate our downloaded layer
RUN go env -w GOPROXY="https://goproxy.cn,direct" && go mod download

# Copy the go source
COPY main.go main.go

# Build
RUN GOPROXY="https://goproxy.cn,direct" CGO_ENABLED=0 GOOS=linux GOARCH=arm64 GO111MODULE=on go build -a -o hello main.go

#FROM openfunction/distroless-static:nonroot
FROM gcr.io/distroless/static-debian11:nonroot-arm64
WORKDIR /
COPY --from=builder /workspace/hello .
USER 65532:65532

ENTRYPOINT ["/hello"]
