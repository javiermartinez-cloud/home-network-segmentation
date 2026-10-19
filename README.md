# Home Network Segmentation
Home network segmentation, firewall rules, and a CGNAT troubleshooting write-up. Applying to the Martinez Home Network. 

## Goal

Split my home network into separate segments so that guest and smart-home
devices can reach the internet but not my NAS or personal computers. This
repo documents the design, the firewall rules that enforce it, how I tested
that the isolation works, and how I diagnosed remote access failing behind
my ISP's carrier-grade NAT (CGNAT).


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

**Most likely cause:** UPnP is disabled and no port forward exists, so the
router drops inbound connections. From the outside, that looks the same
as CGNAT.

**Resolution:** I chose not to open a port. See the port forwarding
decision under Decisions and trade-offs.

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


