FROM golang:1.11-alpine as builder

# Install build dependencies such as git and glide.
RUN apk add --no-cache git gcc musl-dev

WORKDIR $GOPATH/src/github.com/btcsuite/btcd

# Grab and install the latest version of of btcd and all related dependencies.
RUN git clone https://github.com/btcsuite/btcd.git . \
    &&  GO111MODULE=on go install -v . ./cmd/...

# Start a new image
FROM alpine as final

# Expose mainnet ports (server, rpc)
EXPOSE 8333 8334

# Expose testnet ports (server, rpc)
EXPOSE 18333 18334

# Expose simnet ports (server, rpc)
EXPOSE 18555 18556

# Expose segnet ports (server, rpc)
EXPOSE 28901 28902

# Copy the compiled binaries from the builder image.
COPY --from=builder /go/bin/addblock /bin/
COPY --from=builder /go/bin/btcctl /bin/
COPY --from=builder /go/bin/btcd /bin/
COPY --from=builder /go/bin/findcheckpoint /bin/
COPY --from=builder /go/bin/gencerts /bin/

COPY "start-btcctl.sh" .
COPY "start-btcd.sh" .
COPY "uptime.sh" .

RUN mkdir "/root/.btcd" 
COPY "btcd.conf" /root/.btcd/

RUN apk add --no-cache \
    bash \
    ca-certificates \
    && mkdir "/rpc" "/root/.btcctl" \
    && chmod +x start-btcctl.sh \
    && chmod +x start-btcd.sh \
    && rm -f /rpc/rpc.key \
    && rm -f /rpc/rpc.cert \
    # Manually generate certificate and add all domains, it is needed to connect
    # "btcctl" and "lnd" to "btcd" over docker links.
    && "/bin/gencerts" --host="*" --host="btcd" --directory="/rpc" --force

# &&  touch "/root/.btcd/btcd.conf" \

# Create a volume to house pregenerated RPC credentials. This will be
# shared with any lnd, btcctl containers so they can securely query btcd's RPC
# server.
# You should NOT do this before certificate generation!
# Otherwise manually generated certificate will be overridden with shared
# mounted volume! For more info read dockerfile "VOLUME" documentation.
VOLUME ["/rpc"]
