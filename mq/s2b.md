## Run nsqd on one node and nsqadmin on another

nsqd on first node (192.168.56.101)

> ```nsqd --broadcast-address=192.168.56.101```

nsqadmin on second node (192.168.56.102)

> ```nsqadmin --nsqd-http-address=192.168.56.101:4151```
