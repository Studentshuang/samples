FROM golang:1.16 AS builder

WORKDIR /workspace
# Copy the Go Modules manifests
COPY go.mod go.mod
COPY go.sum go.sum
# cache deps before building and copying source so that we don't need to re-download as much
# and so that source changes don't invalidate our downloaded layer
RUN go env -w GOPROXY="https://goproxy.cn,direct" && go mod download 
RUN go mod download github.com/OpenFunction/functions-framework-go
RUN go get github.com/OpenFunction/functions-framework-go/context@v0.5.0
RUN go mod tidy

# Copy the go source
COPY hello.go hello.go
ENV CGO_ENABLED=0
RUN GOPROXY="https://goproxy.cn,direct" CGO_ENABLED=0 GOOS=linux GOARCH=arm64 GO111MODULE=on go build \
    -tags netgo \
    -ldflags "-s -w -extldflags '-static'" \
    -o hellow \
    hello.go

FROM scratch
WORKDIR /
COPY --from=builder /workspace/hello ./
USER 65532:65532

ENTRYPOINT ["./hello"]
