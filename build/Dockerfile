FROM ghcr.io/slok/kube-code-generator:v1.27.0 as builder
WORKDIR /go/src/github.com/sredevopsdev/kafka-connect-k8s-operator

COPY --chown=codegen:codegen . .
ENV CODEGEN_PKG /go/src/k8s.io/code-generator
# Re-generate generated code
RUN hack/update-codegen.sh
USER root
ENV GOOS=linux \
    CGO_ENABLED=0
RUN go build -o build/_output/bin/Kafka-Connect-K8s-Operator \
    -gcflags all=-trimpath=/go/src/github.com/sredevopsdev \
    -asmflags all=-trimpath=/go/src/github.com/sredevopsdev \
    github.com/sredevopsdev/kafka-connect-k8s-operator/cmd/manager

FROM alpine
ENV OPERATOR=/usr/local/bin/Kafka-Connect-K8s-Operator \
    USER_UID=1001 \
    USER_NAME=Kafka-Connect-K8s-Operator

COPY --from=builder /go/src/github.com/sredevopsdev/kafka-connect-k8s-operator/build/_output/bin/Kafka-Connect-K8s-Operator ${OPERATOR}
COPY build/bin /usr/local/bin

RUN  /usr/local/bin/user_setup
ENTRYPOINT ["/usr/local/bin/entrypoint"]
USER ${USER_UID}