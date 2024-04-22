FROM lukemathwalker/cargo-chef:latest AS chef
WORKDIR /app

FROM chef AS planner
COPY . .
RUN cargo chef prepare --recipe-path recipe.json

FROM chef AS builder
COPY --from=planner /app/recipe.json recipe.json
# Build dependencies - this is the caching Docker layer!
RUN cargo chef cook --release --recipe-path recipe.json
# Build application
COPY . .
RUN cargo install --path client_receiver

FROM debian:bookworm-slim as service
COPY --from=builder /usr/local/cargo/bin/registry /usr/local/bin/registry
ENV ROCKET_ADDRESS=0.0.0.0
EXPOSE 8000
CMD ["client_receiver"]
