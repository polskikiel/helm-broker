FROM alpine:3.10 as certs
RUN apk update && apk --no-cache add ca-certificates

FROM eu.gcr.io/kyma-project/health-check-proxy:PR-6466 as health

FROM alpine:3.10 as builder
RUN apk add --no-cache git openssh mercurial

FROM scratch

ENV PATH=/usr/bin
ENV LD_LIBRARY_PATH=/usr/lib

COPY ./controller /root/controller
COPY --from=health /health-check /health-check
COPY --from=certs /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt

# Binaries
COPY --from=builder /usr/bin/ssh /usr/bin/ssh
COPY --from=builder /usr/bin/ssh-add /usr/bin/ssh-add
COPY --from=builder /usr/bin/ssh-agent /usr/bin/ssh-agent
COPY --from=builder /usr/bin/hg /usr/bin/hg
COPY --from=builder /usr/bin/git /usr/bin/git

# Libraries
COPY --from=builder /lib /lib
COPY --from=builder /usr/lib /usr/lib
COPY --from=builder /usr/share/git-core /usr/share/git-core
COPY --from=builder /usr/libexec/git-core /usr/libexec/git-core

ADD tmp /tmp

LABEL source=git@github.com:kyma-project/helm-broker.git

CMD ["/root/controller"]
