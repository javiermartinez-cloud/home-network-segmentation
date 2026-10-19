# Home Network Segmentation
Home network segmentation, firewall rules, and media server remote access troubleshooting write-up. Applying to the Martinez Home Network. 

## Goal

Split my home network into separate segments so that guest and smart-home
devices can reach the internet but not my NAS or personal computers. This
repo documents the design, the firewall rules that enforce it, how I tested
that the isolation works, and how I diagnosed why remote access to my media server is failing.


## Network diagram

## Segments

## Firewall rules

## Verification

## Troubleshooting: was I really behind CGNAT???
**Symptom:** Remote access to my media server from outside the house
failed. I assumed my ISP used carrier-grade NAT (CGNAT), which would block
all inbound connections, so I set up Tailscale as a workaround.

**Hypothesis:** If I'm behind CGNAT, my router's WAN address will fall in
the shared range 100.64.0.0/10, and the internet will see a different
address than my router has.

## Troubleshooting: was I really behind CGNAT?

**Background:** About a year ago I made my media server reachable from
outside the house. I first routed it through Cloudflare's proxy on my own
domain, until I learned Cloudflare's terms don't allow streaming video
through its free plan. I then switched to dynamic DNS and a port forward
on my router. Within about a month, the traffic stats showed constant
scanning from across the internet, so I removed the port forward and
moved remote access to Tailscale.

**Symptom:** In October 2026 I turned on the media server's built-in
remote access, and it failed. I had forgotten removing the port forward
and assumed my ISP used carrier-grade NAT (CGNAT), which blocks all
inbound connections.

**Hypothesis:** If I'm behind CGNAT, my router's WAN address will fall in
the shared range 100.64.0.0/10, and the internet will see a different
address than my router has.

**Tests and evidence:**

1. **Routing table.** The router's WAN interface sits in a public /23
   subnet, not in 100.64.0.0/10.
2. **Traceroute to 1.1.1.1.** The hop after my router is an ordinary
   public ISP address. Behind CGNAT, I'd expect a 100.64.x.x hop there.

```
    1  router (192.168.8.1)        4.392 ms
    2  [ISP router, public IP]     15.893 ms
    3  [ISP core router]           23.639 ms
```

3. **External IP check.** `curl -4 ifconfig.me` returned an address
   inside my router's WAN subnet, so the internet sees my router's own
   address.

**Conclusion:** I'm not behind CGNAT. My ISP gives the router a public
IPv4 address.

**Cause:** UPnP is disabled, and the port forward I removed a year
earlier is gone, so the router drops inbound connections. From the
outside, that looks the same as CGNAT.

**Resolution:** Everything stays closed. Remote access runs through
Tailscale. See the port forwarding decision under Decisions and
trade-offs.

**Note:** The public address is dynamic and can change, which matters for
connecting by raw IP address but isn't what caused this failure.

## Decisions and trade-offs
**No port forwarding for remote media access.** My ISP gives me a public
IP, so forwarding port 32400 would work, but it would expose the media
server to the entire internet. I use Tailscale instead, which reaches the
server through an encrypted tunnel without opening any inbound ports.
UPnP stays disabled so no device can open ports on its own.

## What broke

## What I'd improve


