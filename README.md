# Tailscale DNS Container

This is a dead simple container designed to solve a problem likely unique to
my own network and configuration.

## The Problem

When my devices are on my home network, a DNS server is handed out to them via
DHCP.  Besides the ad-blocking, the server also rewrites requests for
my own domain name to point to the local IP of the server hosting, rather than
providing the DDNS updated DNS record information.  So the devices connect directly
to the server when inside my network using a 192.168.1.xxx address.  This
works great when connected locally.

Now, I'm working on getting Tailscale setup and configured for my devices, so
there will not be open ports and no need for the DDNS updater any longer.
Tailscale allows for DNS control, including using a node as a DNS server.
Great!  But it doesn't work quite how I want, as my existing DNS server will
return the server IP address in the 192.168.1.xxx for my own domain.

To use my existing DNS server, I would also need to advertise subnet routes
to allow access to the server, since the DNS rewrite returns an IP not in the Tailnet.

## The Solution

This simple little container is meant to run alongside a non-ephemeral Tailscale container
and return the Tailscale IP for my domain for requests to my domain, while forwarding
other requests to the existing DNS server.

The end result?  A client connected locally will see the local server IP address.
A client connected via Tailscale sees the Tailscale IP address of the server.  No
subnet routing required.

## Technologies

This image is built on:

- [Alpine Linux](https://hub.docker.com/_/alpine/)
- [s6-overlay](https://github.com/just-containers/s6-overlay)
- [dnsmasq](https://thekelleys.org.uk/dnsmasq/doc.html)
