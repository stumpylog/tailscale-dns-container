# Tailscale DNS Container

This is a dead simple container containing dnsmasq from
Alpine Linux, designed to run connected to your Tailnet and
provide more control over DNS requests.

## The Problem

My home network runs a local DNS server for two purposes: ad-blocking, and
rewriting my own domain name to its local IP address (`192.168.1.xxx`) rather
than the external DDNS address. This means devices on my home network connect
directly to local servers without going out to the internet and back.

When a device connects via [Tailscale](https://tailscale.com/), it no longer
uses the home DNS server, so it resolves my domain to the external DDNS address
instead of the server's Tailnet IP. I wanted Tailscale-connected devices to get
the Tailnet IP for my domain, keeping traffic on the Tailnet rather than routing
it over the internet.

## The Solution

This simple little container is meant to run alongside a non-ephemeral Tailscale container
and return the Tailscale IP for my domain for requests to my domain, while forwarding
other requests to the existing DNS server.

The end result? A client connected locally will see the local server IP address.
A client connected via Tailscale sees the Tailscale IP address of the server. No
subnet routing required.

## Image

```bash
ghcr.io/stumpylog/tailscale-dns-container:latest
```

## Configuration

See the example [docker-compose.yml](./docker-compose.yml) for a full example
of setting the container up, alongside a Tailscale image.

Mount a dnsmasq configuration file into `/etc/dnsmasq.d/`:

```yaml
volumes:
  - "./config/:/etc/dnsmasq.d/:ro"
```

Set your domain with its Tailnet IP as the return value, and set your preferred
upstream DNS for all other requests. This might be a public resolver like
Cloudflare or Google, your own resolver, or something else entirely.

```ini
# Add domains which you want to force to an IP address here.
# Set tailnet IP(s) here
address=/myawesomedomain.me/100.x.y.z

# Add other name servers here, with domain specs if they are for
# non-public domains.
server=1.1.1.1
```

## Technologies

This image is built on:

- [Alpine Linux](https://www.alpinelinux.org/)
- [s6-overlay](https://github.com/just-containers/s6-overlay)
- [dnsmasq](https://thekelleys.org.uk/dnsmasq/doc.html)

## Supported Platforms

- `linux/amd64`
- `linux/arm64`
