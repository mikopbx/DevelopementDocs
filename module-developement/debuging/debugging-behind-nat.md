# Debugging behind NAT

It is relevant if the PhpStorm development environment works for NAT, and PBX is available at a public address.

* "**pbx.address.com**" - the address of MikoPBX&#x20;

On the PC where PhpStorm is running, run the command:

```
ssh -R 9000:127.0.0.1:9000 root@pbx.address.com
```

next, run debugging on MikoPBX:

```
# debugging services
pbx-console debug WorkerCdr 127.0.0.1

# debugging php scripts
xdebug-enable 127.0.0.1

# debugging php-agi scripts
xdebug-enable-agi 127.0.0.1

# debugging php REST API
xdebug-enable-www 127.0.0.1
```
