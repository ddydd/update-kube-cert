# update-kube-cert

更新kubeadm生成的证书有效期为10年  

kubeadm生成的证书有效期为为1年，虽然`1.8`版开始提供了证书生成命令（kubeadm alpha phase certs <cert_name>，`1.13`版开始改为kubeadm init phase certs <cert_name>）

但是`1.11`版之前执行kubeadm alpha phase certs <cert_name>命令，连不上dl.k8s.io会报错  
```
unable to get URL "https://dl.k8s.io/release/stable-1.11.txt": Get https://dl.k8s.io/release/stable-1.11.txt: dial tcp 35.201.71.162:443: i/o timeout
```
从而无法单纯只更新证书  

（`1.12`版之后连不上dl.k8s.io也会执行，`1.12`之后版可以使用`kubeadm`来更新证书，`1.15`版之后增加了证书更新命令，更新证书更加方便了）  

但是你还是****旧版 kubeadm**本，那么可以用这个脚本来更新你的证书，生成证书默认有效期为10年（3650天），你可以更改脚本里面的`CAER_DAYS`变量来达到你想要的证书有效期，单位是“天”  
（kubeadm默认生成是ca有效期是10年，你把`CAER_DAYS`改成太大也没用，因为你master相关证书过期的时候已经过去一年了，ca只剩下9年的有效期了）

更新的证书和kubeconf文件如下  
```
/etc/kubernetes
├── admin.conf
├── controller-manager.conf
├── scheduler.conf
└── pki
    ├── apiserver.crt
    ├── apiserver-etcd-client.crt
    ├── apiserver-kubelet-client.crt
    ├── front-proxy-client.crt
    └── etcd
        ├── healthcheck-client.crt
        ├── peer.crt
        └── server.crt
```

（kubelet证书快过期的时候会自动更新，通常不会和master相关证书一起过期）  

脚本只更新证书，key使用原来的key  

- **更新etcd证书和master证书**   
  如果master和etcd在同一个节点，执行以下命令更新证书etcd证书和master证书，如果你有多个master节点，那么在每个节点都执行一次  
  ```
  ./update-kubeadm-cert.sh
  ```

- **只更新etcd证书**  
  如果你有多个etcd节点，在每个节点上都执行一次  
  ```
  ./update-kubeadm-cert.sh etcd
  ```

- **只更新master证书**  
  如果你有多个master节点，那么在每个节点都执行一次  
  ```
  ./update-kubeadm-cert.sh master
  ```


# 更新已过期证书
在master节点执行，如果你有多个master节点  
```
./update-kubeadm-cert.sh
```

# 证书未过期，更新证书
如果你使用kubeadm生成的证书未过期，比如你更刚安装完毕集群的时候，也可以用此脚本来更新你的证书有效期为10年。  
```
./update-kubeadm-cert.sh
```