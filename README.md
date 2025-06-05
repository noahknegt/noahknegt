# Hello, I'm Noah 👋

## Software Engineer

I'm a software engineer passionate about systems-level programming, embedded Linux, and performance optimization.

---

### 🦀 Programming Languages

```rust
fn main() {
    let languages = vec![
        ("C", "Embedded, drivers, performance"),
        ("C++", "Cross-platform, modern C++ standards"),
        ("Rust", "Memory-safe systems, concurrent programming"),
    ];
}

```

---

### 🐧 Linux Expertise

```bash
# Advanced: kernel debugging, systemd custom units, infra automation
sudo perf record -g -- ./my_app
systemctl status my-custom.service
ansible-playbook --tags "deploy,upgrade" site.yml
```

---

### 📦 Podman (Quadlet) Mastery

```ini
# Rootless container with custom network, isolation, and healthchecks
[Container]
Image=custom/ci-build:latest
Name=ci_build_runner
Network=podman
Environment=CI=true
Volume=%h/build:/build:Z
HealthCheckCmd=build.sh --health
Exec=build.sh --target release
```

---

### 📦 OCI-Compliant Containerfile

```dockerfile
# OCI-compliant multi-stage build for secure, minimal containers
# Build stage
FROM rust:1.70-slim AS builder
WORKDIR /build
COPY Cargo.toml Cargo.lock ./
COPY src ./src

# Create empty dummy project to cache dependencies
RUN mkdir -p src && \
    echo "fn main() {println!(\"if you see this, the build failed\")}" > src/main.rs && \
    cargo build --release && \
    rm -f target/release/deps/myapp* src/main.rs

# Build the actual application
COPY . .
RUN cargo build --release --locked && \
    cargo audit && \
    cargo clippy -- -D warnings

# Runtime stage with minimal distro
FROM debian:stable-slim AS runtime
LABEL org.opencontainers.image.source="https://github.com/noah-knegt_priva/repo" \
      org.opencontainers.image.created="2025-05-23T07:56:29Z" \
      org.opencontainers.image.authors="noah-knegt_priva" \
      org.opencontainers.image.title="Secure Application Container"

# Install minimal runtime dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates tzdata && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set up non-root user in the runtime image
RUN groupadd -g 65535 appuser && \
    useradd -u 65535 -g appuser -s /usr/sbin/nologin -c "Container User" appuser

# Copy the compiled binary from the builder
COPY --from=builder /build/target/release/myapp /usr/local/bin/myapp

# Create data directory with correct permissions
RUN mkdir -p /data && \
    chown 65535:65535 /data

# Set secure permissions on binary
RUN chmod 550 /usr/local/bin/myapp

# Configure runtime environment
ENV RUST_LOG=info

# Run as non-root user
USER appuser

# Mount points for external data
VOLUME ["/data"]

# Set resource limits
STOPSIGNAL SIGTERM

# Health check configuration
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD ["/usr/local/bin/myapp", "--health"]

# Container entrypoint
ENTRYPOINT ["/usr/local/bin/myapp"]
CMD ["--config", "/data/config.toml"]
```

---

### 🧩 Yocto Project

```bitbake
# Modern layer config, PACKAGECONFIG, and image customization
SUMMARY = "Custom system image with extra features"
LICENSE = "MIT"
inherit core-image

IMAGE_FEATURES += "ssh-server-openssh tools-debug"
PACKAGECONFIG:pn-my-custom-app = "feature-x"

IMAGE_INSTALL:append = " my-custom-app"
```

---

### 🧰 Additional Areas of Expertise

- Systems programming & performance tuning
- Embedded Linux (device drivers, real-time)
- Container security & automation (rootless, CI/CD)
- Custom build pipelines for resource-constrained targets

---

💬 Always open to collaboration on robust, efficient, and secure systems!
