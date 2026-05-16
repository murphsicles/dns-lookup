# @net/dns-lookup

[![@net/dns-lookup](https://img.shields.io/badge/zorb-%40net%2Fdns--lookup-blue)](https://zorbs.io)

**DNS resolution for Zeta — discover peers by hostname**

Ported from [dns-lookup v3.0.1](https://crates.io/crates/dns-lookup).

Resolves hostnames to IP addresses for P2P networking, seed node discovery, and general network operations. Maps to the Zeta runtime's host networking layer (getaddrinfo on Linux, similar on other platforms).

## Why DNS Lookup?

Blockchain nodes discover each other through **DNS seeds** — hardcoded hostnames that resolve to full node addresses. Without DNS resolution, P2P networks can't bootstrap:

- 🔗 **Seed node discovery** — resolve `seed.bitcoin.org` to actual peers
- 🌐 **IPv4 + IPv6** — full dual-stack support
- 🔄 **Reverse DNS** — resolve IP addresses back to hostnames
- ⚡ **Low overhead** — thin wrapper over system DNS

## Installation

```toml
[dependencies]
"@net/dns-lookup" = "3.0.1"
```

## Usage

```zeta
const dns = @import("@net/dns-lookup");

// Resolve a hostname to all addresses
const addrs = try dns.lookup_host("seed.bitcoin.org");
for addrs {
    std.log.info("resolved: {s}", [.addr.to_string()]);
}

// Get a single address (prefers IPv4)
const addr = try dns.lookup_ip("seed.bitcoin.org");

// IPv4-only resolution
const v4_addrs = try dns.lookup_host_v4("seed.bitcoin.org");

// IPv6-only resolution
const v6_addrs = try dns.lookup_host_v6("seed.bitcoin.org");

// Reverse DNS
const hostname = try dns.lookup_addr(&addr);
std.log.info("reverse: {s}", [.hostname]);

// Check address types
if addr.is_ipv4() {
    // It's an IPv4 address
} else if addr.is_ipv6() {
    // It's an IPv6 address
}
```

## API

| Function | Description |
|----------|-------------|
| `lookup_host(hostname)` | Resolve hostname to all IPs |
| `lookup_ip(hostname)` | Resolve to single address (prefer IPv4) |
| `lookup_host_v4(hostname)` | IPv4 only |
| `lookup_host_v6(hostname)` | IPv6 only |
| `lookup_addr(ip)` | Reverse DNS (IP → hostname) |

### Types

| Type | Description |
|------|-------------|
| `IpAddr::V4([u8; 4])` | IPv4 address |
| `IpAddr::V6([u8; 16])` | IPv6 address |
| `LookupError` | DNS error (NotFound, TemporaryFailure, SystemError) |

## nour Integration

**DNS seed discovery** is how nour finds peers on the BSV network. Instead of hardcoding IPs (which change), nour stores DNS seeds and resolves them at connection time. This crate provides that capability.

## License

MIT — see [LICENSE](LICENSE)
