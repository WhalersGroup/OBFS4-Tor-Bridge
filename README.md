# OBFS4-Tor-Bridge
How to stop worrying and love the TOR

## Server Configuration
```bash
apt install tor obfs4proxy

nano /etc/tor/torrc
```

```bash
deb     [signed-by=/usr/share/keyrings/tor-archive-keyring.gpg] https://deb.torproject.org/torproject.org focal  main
#deb-src [signed-by=/usr/share/keyrings/tor-archive-keyring.gpg] https://deb.torproject.org/torproject.org focal  main
```
### TORRC FILE
```bash

RunAsDaemon 1
ORPort 6889
ExtORPort auto
ExitPolicy reject *:*
BridgeRelay 1
PublishServerDescriptor 0
ServerTransportPlugin obfs4 exec /usr/bin/obfs4proxy
ServerTransportListenAddr obfs4 0.0.0.0:443
ContactInfo yourname@example.com
Nickname FreeBeerPrivate

```

```bash

RunAsDaemon 1
ORPort 36779
ExtORPort auto
ExitPolicy reject *:*
BridgeRelay 1
PublishServerDescriptor 0
ServerTransportPlugin obfs4 exec /usr/bin/obfs4proxy
ServerTransportListenAddr obfs4 0.0.0.0:34558
ContactInfo jackofwhalers@protonmail.com
Nickname masterdaud

```

### Port Configuration

If you want to use ports lower than 1024

```bash
apt install libcap2-bin
setcap cap_net_bind_service=+ep /usr/bin/obfs4proxy
```

### Systemd Configuration

```bash
nano /lib/systemd/system/tor@default.service

Change the line NoNewPrivileges=yes to read NoNewPrivileges=no.

```

```bash

nano /lib/systemd/system/tor@.service

Also change here the line NoNewPrivileges=yes to read NoNewPrivileges=no.

```

### Obtain Fingerprint & Client-Configuration

```bash
cat /var/lib/tor/pt_state/obfs4_bridgeline.txt

See Bridge obfs4 <IP ADDRESS>:<PORT> <FINGERPRINT> cert=BO53...jHXA iat-mode=0

cat /var/lib/tor/fingerprint

FreeBeerPrivate EF2EB5F901234567ABCDEF906238BC63ABCDEF28
```

### Systemd Service

```bash
[Unit]
Description=Anonymizing overlay network for TCP
After=network-online.target nss-lookup.target
PartOf=tor.service
ReloadPropagatedFrom=tor.service

[Service]
Type=notify
NotifyAccess=all
PIDFile=/run/tor/tor.pid
PermissionsStartOnly=yes
ExecStartPre=/usr/bin/install -Z -m 02755 -o debian-tor  -g tor-users -d /run/tor
ExecStartPre=/usr/bin/tor --defaults-torrc /usr/share/tor/tor-service-defaults-torrc -f /etc/tor/torrc --RunAsDaemon 0 --verify-config
ExecStart=/usr/bin/tor --defaults-torrc /usr/share/tor/tor-service-defaults-torrc -f /etc/tor/torrc --RunAsDaemon 0
ExecReload=/bin/kill -HUP ${MAINPID}
KillSignal=SIGINT
TimeoutStartSec=300
TimeoutStopSec=60
Restart=on-failure
LimitNOFILE=65536
MemoryMax=300M
# Hardening
AppArmorProfile=-system_tor
NoNewPrivileges=yes
PrivateTmp=yes
PrivateDevices=yes
ProtectHome=yes
ProtectSystem=full
ReadOnlyDirectories=/
ReadWriteDirectories=-/proc
ReadWriteDirectories=-/var/lib/tor
ReadWriteDirectories=-/var/log/tor
ReadWriteDirectories=-/run
CapabilityBoundingSet=CAP_SETUID CAP_SETGID CAP_NET_BIND_SERVICE CAP_DAC_READ_SEARCH

```

