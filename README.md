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

## Troubleshooting: remote access behind CGNAT
Loking at the routing table and running a traceroute to test the CGNAT theory. 
**Most likely cause:** UPnP is disabled and no port forward exists, so the
router drops inbound connections. From the outside, that looks the same
as CGNAT.

## Decisions and trade-offs
**No port forwarding for remote media access.** My ISP gives me a public
IP, so forwarding port 32400 would work, but it would expose the media
server to the entire internet. I use Tailscale instead, which reaches the
server through an encrypted tunnel without opening any inbound ports.
UPnP stays disabled so no device can open ports on its own.
## What broke

## What I'd improve


