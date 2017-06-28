## NSQ Scenarios

Assumption: The following scenarios assume you are running nsqd and/or its components on one 
or more Turnkey Linux VMs (virtual machines) using host-only networking.

### Scenario 1: Run nsqd and nsqadmin on one node

Assumption: ip address = 192.168.56.101


1) Run ```nsqd```

Notes: 

- by default, nsqd listens on all network interfaces
- by default, nsqd listens on port 4151 for HTTP clients like nsqadmin

2) Run nsqadmin daemon

- nsqadmin requires --nsqd-http-address or --lookupd-http-address

```nsqadmin --nsqd-http-address=192.168.56.101:4151```


3) Run nsqadmin UI

On your Web browser, type ```http://192.168.56.101:4171```
