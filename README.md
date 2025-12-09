# Tailscale DNS Container

This is a dead simple container containing dnsmasq from
Alpine Linux, designed to run connected to your Tailnet and
provide more control over DNS requests.

## The Problem

When my devices are on my home network, a DNS server is handed out to them via
DHCP. Besides the ad-blocking, the server also rewrites requests for
my own domain name to point to the local IP of the server hosting, rather than
providing the DDNS updated DNS record information. So the devices connect directly
to the server when inside my network using a 192.168.1.xxx address. This
works great when connected locally.

I wanted roughly the same thing to happen when a device is connected via
[Tailscale](https://tailscale.com/). When a device is on the my tailnet, it
receives the server IP address for my domain as being the server's tailnet IP
address. For requests not for my own domain, they are passed upstream to
AdGuard (or any other DNS server).

## The Solution

This simple little container is meant to run alongside a non-ephemeral Tailscale container
and return the Tailscale IP for my domain for requests to my domain, while forwarding
other requests to the existing DNS server.

The end result? A client connected locally will see the local server IP address.
A client connected via Tailscale sees the Tailscale IP address of the server. No
subnet routing required.

## Configuration

See the example [docker-compose.yml](./docker-compose.yml) for a full example
of setting the container up, alongside a Tailscale image.

Mount a dnsmasq configuration file into `/etc/dnsmasq.d/`. Set your domain with its
Tailnet IP as the return value.

Set your preferred upstream DNS for all other requests. This might be a public
resolver like Cloudflare or Google, your own resolver or something else entirely.

```
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
