---
layout: post
title: "Quick and Brief Homelab Overview"
date: 2026-09-16
---

I get asked, or at least used to get asked, quite a bit on how I have my homelab setup. My lab has changed overtime quite a bit. I think the typical bell curve of homelabbing, start small, get real into it and get all the things, then tone it back down. I think the toned down part is generally the more interesting piece, at least to me. It was the point where I've distilled what was valuable to me and what was just extra; no need for blinkenlights.

So here's roughly how I deal with it:

## Router / Gateway / Firewall / Edge Device

Whatever you want to call it, the part of your network where it first comes in and is the last device to see the traffic leave. There are plenty of options and I've dealt with plenty as I've worked through what I like:

- pfSense
- OpnSense
- VyOS
- Unifi
- MikroTik
- TP-Link

As I experimented with them over the years, stuff shook out in various ways.

***My current model:***

I don't need much. Anything that's going to allow me to separate LANs into VLANs and with firewall rules in between those VLANs is likely good enough for what I need it to do. This will make more sense later.

## Switch

Similar to above, I've tested a bunch. This is probably one of the least interesting ones, where I don't have a need to have a giga-switch. I've previously used L3 switches, but honestly, not having VLAN traversals hit a legitimate firewall feels gross. I also have played in the land of 10GB networking, and it's just one of those things that I don't need. I'm not doing things like shared storage between clustered hypervisors or doing Ceph on HVCI devices.

***My current model:***

A switch that supports VLANs. PoE on some ports is a plus.

## Access Points

Another boring one. I just need one that works. 2.4GHz/5GHz that doesn't cause people to scream at me. Unifi has been pretty good for this.

***My current model:***

APs that are at least WiFi 5, with a 2.4GHz band. I like a wired backhaul. Be able to have separate SSIDs and deal with VLANs or Everything else is probably a plus.

## Hypervisors

I've tried them all. They're all good in different ways. Boring. Except Ludus. Ludus is awesome.

***My current model:***

I prefer the CLI and I prefer things to either be defined or automated. If those are met, my hypervisor needs are not crazy outside of when I want to spin things up and down. And Ludus. But that only gets spun up if I have something I actually need to test.

## Security

I think this is generally the reason why I get asked the question a lot. Some of course is to see how I've utilized the resources that I've been provided, and peers want to evaluate. Cool. Others want to see how over the top that I've gotten because I think I strike some people that way. Maybe. Reasonable, as this is where most of my thinking ends up when dealing with my lab or personal use.

Here's a rough overview of how I have my lab setup:

- Gateway
    - VLANS
    - Firewall
        - Firewall blocks all traffic between VLANs by default, and Tailscale is allowed between a couple.
        - Others are just blocked in general for things like IoT or devices that just don't need to be talking to one another.
        - mDNS between certain subnets with specifics protocols allowed.
- Tailscale
    - Between personally maintained devices and lab devices
    - ACLs in place with roles to ensure only those that should be talking, and on what they should be talking about, is allowed.
- Hosts
    - Host based firewalls turned on
        - Typical network traffic is denied, only the Tailscale interface allows access.
    - Managed via Ansible playbooks, which prevents some config drift

I'm likely missing some stuff, so I'm gonna ramble. My goal is to attempt to minimize the blast radius of a compromise of any given device. Gateway device popped? Nothing else responds to traffic that would remediate it. Compromised endpoint? Can't get to the Gateway without a physically off jump box that is only on when I need it on. Compromised the lab? Tailscale ACLs say no to devices that shouldn't be talked to.

Layering broader VLANs and firewalls with an authenticated only overlay network makes a pretty sufficient way to deal with this. Since auth can be easily tied to hardware keys, it makes it quite a bit harder to hit those tunnels. If you get to one thing, it either isn't important, doesn't have internet access, or is going to get wiped. Some things in my network can just be reset and put back randomly anyways.

Monitoring and canaries are pretty good too. Canaries are real cheap and easy.

kbye
