---
title: "Adding DNS Records on Keenetic Routers"
summary: "Custom DNS records on Keenetic without a separate DNS server"
date: 2026-10-20T10:00:00+02:00
tags:
  - linux
  - shell
draft: false
---

Keenetic routers let you add DNS records without setting up a separate DNS server.

1. Connect to the router.
```console
$ telnet <router ip address>
<enter login and password>
```

2. Add a DNS record for the domain.
- Replace `<domain>` with the domain you need.
- Replace `<ip>` with the IP address you want to map to the domain.

```console
$ ip host <domain> <ip>
$ system configuration save
```

3. To delete a DNS record, use the `no ip host` command.
```console
$ no ip host <domain>
$ system configuration save
```
