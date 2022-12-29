# If you change this value, please change it in the following files as well:
# /.travis.yml
# /dev.Dockerfile
# /make/builder.Dockerfile
# /.github/workflows/main.yml
# /.github/workflows/release.yml
FROM golang:1.19.2-alpine as builder

# Force Go to use the cgo based DNS resolver. This is required to ensure DNS
# queries required to connect to linked containers succeed.
ENV GODEBUG netdns=cgo

# Pass a tag, branch or a commit using build-arg.  This allows a docker
# image to be built from a specified Git state.  The default image
# will use the Git tip of master by default.
ARG checkout="master"
ARG git_url="https://github.com/lightningnetwork/lnd"

# Install dependencies and build the binaries.
RUN apk add --no-cache --update alpine-sdk \
    git \
    make \
    gcc \
&&  git clone $git_url /go/src/github.com/lightningnetwork/lnd \
&&  cd /go/src/github.com/lightningnetwork/lnd \
&&  git checkout $checkout \
&&  make release-install

RUN apk --no-cache add \
    bash \
    jq \
    ca-certificates \
    gnupg \
    curl

RUN sha256sum /go/bin/lnd /go/bin/lncli > /shasums.txt \
  && cat /shasums.txt

# Start a new, final image.
FROM scratch as final

# Define a root volume for data persistence.
VOLUME /root/.lnd

# Add utilities for quality of life and SSL-related reasons. We also require
# curl and gpg for the signature verification script.

# Copy the binaries from the builder image.
COPY --from=builder /go/bin/lnd /bin/
COPY --from=builder /shasums.txt /shasums.txt
COPY --from=builder /go/src/github.com/lightningnetwork/lnd/scripts/verify-install.sh /
COPY --from=builder /go/src/github.com/lightningnetwork/lnd/scripts/keys/* /keys/

# Store the SHA256 hash of the binaries that were just produced for later
# verification.

# Expose lnd ports (p2p, rpc).
EXPOSE 9735 10009

# Specify the start command and entrypoint as the lnd daemon.
ENTRYPOINT ["lnd"]