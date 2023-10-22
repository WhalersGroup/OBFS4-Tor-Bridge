# OBFS4-Tor-Bridge
How to stop worrying and love the TOR

## Server Configuration
```bash
apt install tor obfs4proxy

nano /etc/tor/torrc
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


