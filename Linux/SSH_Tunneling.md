# ASCENDING SSH Port Forwarding/Tunneling Guide

SSH port forwarding is a mechanism in SSH for tunneling application ports from the client machine to the server machine, or vice versa.

## Local Forwarding

Local forwarding is used to forward a port from the client machine to the server machine.

In OpenSSH, local port forwarding is configured using the -L option:

```
$ ssh -L 80:<destination_server_address>:80 <jump_server_address>
```

This example opens a connection to the jump server, and forwards any connection to 80 port on the local machine to port 80 on destination server

For linux servers, you need to change the `/etc/ssh/sshd_config` file for SSH port forwarding to work:

```
# Port forwarding
AllowTcpForwarding yes
```

Restart the SSH service on linux after the change:

```
Ubuntu / Debian: systemctl restart ssh.service
CentOS / RHEL: systemctl restart sshd.service
```