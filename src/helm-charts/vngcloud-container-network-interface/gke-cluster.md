# GKE cluster

```bash
$ k get node -owide
NAME                                       STATUS   ROLES    AGE     VERSION               INTERNAL-IP   EXTERNAL-IP    OS-IMAGE                             KERNEL-VERSION   CONTAINER-RUNTIME
gke-cluster-1-default-pool-07a69eec-7p64   Ready    <none>   6h13m   v1.29.6-gke.1038001   10.128.0.20   34.44.111.30   Container-Optimized OS from Google   6.1.85+          containerd://1.7.15

$ k get pod -A -owide
NAMESPACE                        NAME                                                      READY   STATUS      RESTARTS   AGE     IP            NODE                                       NOMINATED NODE   READINESS GATES
gke-managed-dpv2-observability   hubble-relay-856884f8d-gq8k2                              3/3     Running     0          6h12m   10.28.0.14    gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
gmp-system                       collector-8hm2b                                           2/2     Running     0          6h10m   10.28.0.13    gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
gmp-system                       gmp-operator-f8f7dbc66-djfsh                              1/1     Running     0          6h12m   10.28.0.2     gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      anetd-f4pp6                                               2/2     Running     0          6h11m   10.128.0.20   gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      antrea-controller-horizontal-autoscaler-9df77d778-rjkks   1/1     Running     0          6h12m   10.28.0.4     gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      event-exporter-gke-766bc76558-ddm6l                       2/2     Running     0          6h12m   10.28.0.5     gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      fluentbit-gke-9fb9f                                       3/3     Running     0          6h11m   10.128.0.20   gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      gke-metrics-agent-t878j                                   3/3     Running     0          6h11m   10.128.0.20   gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      hubble-generate-certs-init-n2-q6hnj                       0/1     Completed   0          6h12m   10.28.0.7     gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      konnectivity-agent-6c597c68d6-mkn6d                       2/2     Running     0          6h12m   10.28.0.9     gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      konnectivity-agent-autoscaler-67d4f7d5f-g9hsg             1/1     Running     0          6h12m   10.28.0.6     gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      kube-dns-6788cfd7d4-qwth6                                 5/5     Running     0          6h12m   10.28.0.8     gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      kube-dns-autoscaler-79b96f5cb-qtbnz                       1/1     Running     0          6h12m   10.28.0.10    gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      l7-default-backend-6484dd554-fw4z6                        1/1     Running     0          6h12m   10.28.0.3     gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      metrics-server-v0.7.1-6b8d6d8c46-vbvjq                    2/2     Running     0          6h10m   10.28.0.15    gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      netd-524z6                                                2/2     Running     0          6h11m   10.128.0.20   gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>
kube-system                      pdcsi-node-qp4w7                                          2/2     Running     0          6h11m   10.128.0.20   gke-cluster-1-default-pool-07a69eec-7p64   <none>           <none>

$ k get MutatingWebhookConfiguration
NAME                                                       WEBHOOKS   AGE
gmp-operator.gmp-system.monitoring.googleapis.com          2          6h15m
neg-annotation.config.common-webhooks.networking.gke.io    1          6h15m
pod-ready.config.common-webhooks.networking.gke.io         1          6h15m
warden-mutating.config.common-webhooks.networking.gke.io   1          6h15m

$ k get ValidatingWebhookConfiguration
NAME                                                              WEBHOOKS   AGE
flowcontrol-guardrails.config.common-webhooks.networking.gke.io   1          6h15m
gmp-operator.gmp-system.monitoring.googleapis.com                 7          6h15m
nodelimit.config.common-webhooks.networking.gke.io                1          6h15m
validation-webhook.snapshot.storage.k8s.io                        1          6h15m
warden-validating.config.common-webhooks.networking.gke.io        1          6h15m



# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc mq state UP group default qlen 1000
    link/ether 42:01:0a:80:00:14 brd ff:ff:ff:ff:ff:ff
    inet 10.128.0.20/32 metric 1024 scope global dynamic eth0
       valid_lft 2331sec preferred_lft 2331sec
    inet6 fe80::4001:aff:fe80:14/64 scope link 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:c9:fc:42:45 brd ff:ff:ff:ff:ff:ff
    inet 169.254.123.1/24 brd 169.254.123.255 scope global docker0
       valid_lft forever preferred_lft forever
4: cilium_net@cilium_host: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether 86:cd:3b:5d:a2:1c brd ff:ff:ff:ff:ff:ff
    inet6 fe80::84cd:3bff:fe5d:a21c/64 scope link 
       valid_lft forever preferred_lft forever
5: cilium_host@cilium_net: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether 46:02:b1:80:bb:c7 brd ff:ff:ff:ff:ff:ff
    inet 169.254.4.6/32 scope global cilium_host
       valid_lft forever preferred_lft forever
    inet6 fe80::4402:b1ff:fe80:bbc7/64 scope link 
       valid_lft forever preferred_lft forever
6: gke0b335c00e99@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether 06:e7:e7:e6:af:61 brd ff:ff:ff:ff:ff:ff link-netns cni-f3b38564-59bc-5272-5cfb-2ea12e039bdd
    inet 10.28.0.1/32 scope global gke0b335c00e99
       valid_lft forever preferred_lft forever
    inet6 fe80::4e7:e7ff:fee6:af61/64 scope link 
       valid_lft forever preferred_lft forever
7: gke5edb4f4d5d2@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether 76:d5:f1:eb:10:f0 brd ff:ff:ff:ff:ff:ff link-netns cni-e5248c55-041b-f637-2694-34d8901f5cf1
    inet 10.28.0.1/32 scope global gke5edb4f4d5d2
       valid_lft forever preferred_lft forever
    inet6 fe80::74d5:f1ff:feeb:10f0/64 scope link 
       valid_lft forever preferred_lft forever
8: gke2f0b7056934@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether 96:eb:49:ea:b9:10 brd ff:ff:ff:ff:ff:ff link-netns cni-f36ec1d7-bc9d-f81e-0598-27927e51163b
    inet 10.28.0.1/32 scope global gke2f0b7056934
       valid_lft forever preferred_lft forever
    inet6 fe80::94eb:49ff:feea:b910/64 scope link 
       valid_lft forever preferred_lft forever
9: gkeb4a696f08b2@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether 8a:8c:21:b0:ad:9c brd ff:ff:ff:ff:ff:ff link-netns cni-0d47d93f-5320-0191-8a2f-f4cd8b5f0428
    inet 10.28.0.1/32 scope global gkeb4a696f08b2
       valid_lft forever preferred_lft forever
    inet6 fe80::888c:21ff:feb0:ad9c/64 scope link 
       valid_lft forever preferred_lft forever
10: gkeb4e717fa944@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether 96:8a:1d:04:09:f9 brd ff:ff:ff:ff:ff:ff link-netns cni-92da20b5-b51b-168f-fa89-79e3822ed6fb
    inet 10.28.0.1/32 scope global gkeb4e717fa944
       valid_lft forever preferred_lft forever
    inet6 fe80::948a:1dff:fe04:9f9/64 scope link 
       valid_lft forever preferred_lft forever
12: gke394491cc016@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether be:21:c7:b2:42:32 brd ff:ff:ff:ff:ff:ff link-netns cni-d35414ea-463b-63c5-03cf-39d398fb4a28
    inet 10.28.0.1/32 scope global gke394491cc016
       valid_lft forever preferred_lft forever
    inet6 fe80::bc21:c7ff:feb2:4232/64 scope link 
       valid_lft forever preferred_lft forever
13: gke79f55697404@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether ba:68:47:3e:ea:9c brd ff:ff:ff:ff:ff:ff link-netns cni-0e404dca-9968-5501-f7db-12c547d6c8ee
    inet 10.28.0.1/32 scope global gke79f55697404
       valid_lft forever preferred_lft forever
    inet6 fe80::b868:47ff:fe3e:ea9c/64 scope link 
       valid_lft forever preferred_lft forever
14: gke517aace7104@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether d6:50:f1:2c:0a:15 brd ff:ff:ff:ff:ff:ff link-netns cni-589756e7-49bf-5157-b81d-12041be75c16
    inet 10.28.0.1/32 scope global gke517aace7104
       valid_lft forever preferred_lft forever
    inet6 fe80::d450:f1ff:fe2c:a15/64 scope link 
       valid_lft forever preferred_lft forever
17: gke515bebef38f@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether 76:44:bb:bf:5c:3a brd ff:ff:ff:ff:ff:ff link-netns cni-da9d07b5-875f-5d4e-5447-7cc2bd092e1f
    inet 10.28.0.1/32 scope global gke515bebef38f
       valid_lft forever preferred_lft forever
    inet6 fe80::7444:bbff:febf:5c3a/64 scope link 
       valid_lft forever preferred_lft forever
18: gke1aaa257200f@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether c2:a0:20:8e:24:aa brd ff:ff:ff:ff:ff:ff link-netns cni-e68060f9-a6a1-5b7a-60a6-93f50f71ec35
    inet 10.28.0.1/32 scope global gke1aaa257200f
       valid_lft forever preferred_lft forever
    inet6 fe80::c0a0:20ff:fe8e:24aa/64 scope link 
       valid_lft forever preferred_lft forever
19: gke86de750afbe@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default qlen 1000
    link/ether f2:0a:95:3e:b0:83 brd ff:ff:ff:ff:ff:ff link-netns cni-fce2835a-bb43-f488-05b1-2e6d9e4f3b03
    inet 10.28.0.1/32 scope global gke86de750afbe
       valid_lft forever preferred_lft forever
    inet6 fe80::f00a:95ff:fe3e:b083/64 scope link 
       valid_lft forever preferred_lft forever


# ip netns
cni-fce2835a-bb43-f488-05b1-2e6d9e4f3b03 (id: 5)
cni-e68060f9-a6a1-5b7a-60a6-93f50f71ec35 (id: 11)
cni-da9d07b5-875f-5d4e-5447-7cc2bd092e1f (id: 10)
cni-589756e7-49bf-5157-b81d-12041be75c16 (id: 8)
cni-0e404dca-9968-5501-f7db-12c547d6c8ee (id: 7)
cni-d35414ea-463b-63c5-03cf-39d398fb4a28 (id: 6)
cni-92da20b5-b51b-168f-fa89-79e3822ed6fb (id: 4)
cni-0d47d93f-5320-0191-8a2f-f4cd8b5f0428 (id: 3)
cni-f36ec1d7-bc9d-f81e-0598-27927e51163b (id: 2)
cni-e5248c55-041b-f637-2694-34d8901f5cf1 (id: 1)
cni-f3b38564-59bc-5272-5cfb-2ea12e039bdd (id: 0)


# arp -n
Address                  HWtype  HWaddress           Flags Mask            Iface
10.28.0.15               ether   12:ab:e0:7b:00:f2   C                     gke86de750afbe
10.28.0.6                ether   0e:08:16:65:ef:ef   C                     gkeb4e717fa944
10.28.0.14               ether   2a:27:ad:d1:45:2b   C                     gke1aaa257200f
10.28.0.9                ether   1e:8b:2d:f0:2f:bf   C                     gke517aace7104
10.28.0.4                ether   4a:5e:02:e1:21:ce   C                     gke2f0b7056934
10.28.0.2                ether   06:cb:30:19:68:78   C                     gke0b335c00e99
10.28.0.5                ether   06:e3:bf:b7:5e:05   C                     gkeb4a696f08b2
10.28.0.13               ether   76:a6:f9:4d:d4:1a   C                     gke515bebef38f
10.28.0.10               ether   2e:3c:a3:1e:d4:38   C                     gke79f55697404
10.128.0.1               ether   42:01:0a:80:00:01   C                     eth0
10.28.0.8                ether   9a:6f:5f:1b:76:cb   C                     gke394491cc016
10.28.0.3                ether   2e:c5:03:33:7a:61   C                     gke5edb4f4d5d2


# crictl ps
CONTAINER           IMAGE               CREATED              STATE               NAME                                   ATTEMPT             POD ID              POD
2c4e2280c4f52       a606584aa9aa8       About a minute ago   Running             nsenter                                0                   7004b00d84ce3       nsenter-9ad5k4
992eb2db36575       bb1af7093b4f8       6 hours ago          Running             kubedns-metrics-collector              0                   c628e3d09182b       kube-dns-6788cfd7d4-qwth6
f74322a696a26       6c64a244676e4       6 hours ago          Running             prometheus-to-sd                       0                   c628e3d09182b       kube-dns-6788cfd7d4-qwth6
846ec00221e3a       1d241be96ee26       6 hours ago          Running             config-reloader                        0                   b9fe0f5dac86c       collector-8hm2b
4808254049b20       740e4e5e776d6       6 hours ago          Running             sidecar                                0                   c628e3d09182b       kube-dns-6788cfd7d4-qwth6
4d418645b9418       e7b33bb6b5d62       6 hours ago          Running             prometheus                             0                   b9fe0f5dac86c       collector-8hm2b
56faad850cddd       5d75c53021e00       6 hours ago          Running             metrics-server-nanny                   0                   bfc7e59e05047       metrics-server-v0.7.1-6b8d6d8c46-vbvjq
8dda32b713f1e       3391eedf97133       6 hours ago          Running             dnsmasq                                0                   c628e3d09182b       kube-dns-6788cfd7d4-qwth6
6c114f9e08ca1       557403aa33912       6 hours ago          Running             metrics-server                         0                   bfc7e59e05047       metrics-server-v0.7.1-6b8d6d8c46-vbvjq
93e40ab39ad1e       49238597bfc7d       6 hours ago          Running             hubble-relay-metrics-collector         0                   bc752553ed50f       hubble-relay-856884f8d-gq8k2
2fb2052116e53       e7a49ea2c2e21       6 hours ago          Running             hubble-cli                             0                   bc752553ed50f       hubble-relay-856884f8d-gq8k2
1a869d3a87f57       8b28163dfec20       6 hours ago          Running             prometheus-to-sd-exporter              0                   5f37377543547       event-exporter-gke-766bc76558-ddm6l
356bd963296d6       bb1af7093b4f8       6 hours ago          Running             konnectivity-agent-metrics-collector   0                   0dfa7aa2e1af6       konnectivity-agent-6c597c68d6-mkn6d
10ad97458491f       193ea1d5da271       6 hours ago          Running             hubble-relay                           0                   bc752553ed50f       hubble-relay-856884f8d-gq8k2
70662faac09e8       f93a612da8dbf       6 hours ago          Running             konnectivity-agent                     0                   0dfa7aa2e1af6       konnectivity-agent-6c597c68d6-mkn6d
f429c7173599a       48a442e12d59a       6 hours ago          Running             kubedns                                0                   c628e3d09182b       kube-dns-6788cfd7d4-qwth6
2a710e723b3b2       1a74d6abc6154       6 hours ago          Running             autoscaler                             0                   7cef80ae92897       kube-dns-autoscaler-79b96f5cb-qtbnz
65583bb398b1c       cf9076ec4ba6a       6 hours ago          Running             autoscaler                             0                   56017da4967fe       antrea-controller-horizontal-autoscaler-9df77d778-rjkks
eae0143f5d114       cf9076ec4ba6a       6 hours ago          Running             autoscaler                             0                   138d6b5b622ed       konnectivity-agent-autoscaler-67d4f7d5f-g9hsg
1f3dad1cdd49c       64d861d1042dd       6 hours ago          Running             event-exporter                         0                   5f37377543547       event-exporter-gke-766bc76558-ddm6l
e4c2c9790ce91       b657ed1ed0174       6 hours ago          Running             operator                               0                   75960f7017974       gmp-operator-f8f7dbc66-djfsh
bdf93b646970e       f0f44052f9fac       6 hours ago          Running             default-http-backend                   0                   041592e684ef9       l7-default-backend-6484dd554-fw4z6
c00b331c02b37       475fd44fd7c62       6 hours ago          Running             fluentbit-metrics-collector            0                   6de0875ce203e       fluentbit-gke-9fb9f
7baed51331cc3       aa8534dc5a674       6 hours ago          Running             fluentbit-gke                          0                   6de0875ce203e       fluentbit-gke-9fb9f
df0c612fdd536       d0c207fb0607c       6 hours ago          Running             fluentbit                              0                   6de0875ce203e       fluentbit-gke-9fb9f
2385302b54b41       bb1af7093b4f8       6 hours ago          Running             netd-metrics-collector                 0                   28334fe375f85       netd-524z6
80d8c798dd85e       ca089785d6d3f       6 hours ago          Running             netd                                   0                   28334fe375f85       netd-524z6
bf80cad8aeb10       5dec0071197e6       6 hours ago          Running             gce-pd-driver                          0                   7108d540cd520       pdcsi-node-qp4w7
4ab39618edfe4       49238597bfc7d       6 hours ago          Running             cilium-agent-metrics-collector         0                   108cfbb0cd8ae       anetd-f4pp6
1163c962e6a53       fb7c5e3c7ddbb       6 hours ago          Running             prometheus-metrics-collector           0                   ff2cabbb34637       gke-metrics-agent-t878j
625758ff00e43       1ac6b80bfade0       6 hours ago          Running             cilium-agent                           0                   108cfbb0cd8ae       anetd-f4pp6
65d2794fff7cd       74ebdc3f247fc       6 hours ago          Running             core-metrics-exporter                  0                   ff2cabbb34637       gke-metrics-agent-t878j
2485763976a40       74ebdc3f247fc       6 hours ago          Running             gke-metrics-agent                      0                   ff2cabbb34637       gke-metrics-agent-t878j
5ef5084975368       c223940f67e20       6 hours ago          Running             csi-driver-registrar                   0                   7108d540cd520       pdcsi-node-qp4w7


# ip netns exec cni-fce2835a-bb43-f488-05b1-2e6d9e4f3b03 ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0@if19: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
    link/ether 12:ab:e0:7b:00:f2 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.28.0.15/24 brd 10.28.0.255 scope global eth0
       valid_lft forever preferred_lft forever


# ip netns exec cni-fce2835a-bb43-f488-05b1-2e6d9e4f3b03 arp -n
Address                  HWtype  HWaddress           Flags Mask            Iface
10.28.0.1                ether   f2:0a:95:3e:b0:83   C                     eth0


# ip route
default via 10.128.0.1 dev eth0 proto dhcp src 10.128.0.20 metric 1024 
10.28.0.2 dev gke0b335c00e99 scope link 
10.28.0.3 dev gke5edb4f4d5d2 scope link 
10.28.0.4 dev gke2f0b7056934 scope link 
10.28.0.5 dev gkeb4a696f08b2 scope link 
10.28.0.6 dev gkeb4e717fa944 scope link 
10.28.0.8 dev gke394491cc016 scope link 
10.28.0.9 dev gke517aace7104 scope link 
10.28.0.10 dev gke79f55697404 scope link 
10.28.0.13 dev gke515bebef38f scope link 
10.28.0.14 dev gke1aaa257200f scope link 
10.28.0.15 dev gke86de750afbe scope link 
10.128.0.1 dev eth0 proto dhcp scope link src 10.128.0.20 metric 1024 
169.254.123.0/24 dev docker0 proto kernel scope link src 169.254.123.1 linkdown 
169.254.169.254 via 10.128.0.1 dev eth0 proto dhcp src 10.128.0.20 metric 1024


# ip rule
9:      from all fwmark 0x200/0xf00 lookup 2004
100:    from all lookup local
32766:  from all lookup main
32767:  from all lookup default



# ip r s t 2004
local default dev lo scope host


# iptables -S
-P INPUT DROP
-P FORWARD DROP
-P OUTPUT DROP
-N CILIUM_FORWARD
-N CILIUM_INPUT
-N CILIUM_OUTPUT
-N DOCKER
-N DOCKER-ISOLATION-STAGE-1
-N DOCKER-ISOLATION-STAGE-2
-N DOCKER-USER
-N KUBE-FIREWALL
-N KUBE-KUBELET-CANARY
-A INPUT -m comment --comment "cilium-feeder: CILIUM_INPUT" -j CILIUM_INPUT
-A INPUT -j KUBE-FIREWALL
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -j ACCEPT
-A INPUT -p udp -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -p sctp -j ACCEPT
-A FORWARD -m comment --comment "cilium-feeder: CILIUM_FORWARD" -j CILIUM_FORWARD
-A FORWARD -j DOCKER-USER
-A FORWARD -j DOCKER-ISOLATION-STAGE-1
-A FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -o docker0 -j DOCKER
-A FORWARD -i docker0 ! -o docker0 -j ACCEPT
-A FORWARD -i docker0 -o docker0 -j ACCEPT
-A FORWARD -p tcp -j ACCEPT
-A FORWARD -p udp -j ACCEPT
-A FORWARD -p icmp -j ACCEPT
-A FORWARD -p sctp -j ACCEPT
-A OUTPUT -m comment --comment "cilium-feeder: CILIUM_OUTPUT" -j CILIUM_OUTPUT
-A OUTPUT -j KUBE-FIREWALL
-A OUTPUT -m state --state NEW,RELATED,ESTABLISHED -j ACCEPT
-A OUTPUT -o lo -j ACCEPT
-A CILIUM_FORWARD -o cilium_host -m comment --comment "cilium: any->cluster on cilium_host forward accept" -j ACCEPT
-A CILIUM_FORWARD -i cilium_host -m comment --comment "cilium: cluster->any on cilium_host forward accept (nodeport)" -j ACCEPT
-A CILIUM_FORWARD -i lxc+ -m comment --comment "cilium: cluster->any on lxc+ forward accept" -j ACCEPT
-A CILIUM_FORWARD -i cilium_net -m comment --comment "cilium: cluster->any on cilium_net forward accept (nodeport)" -j ACCEPT
-A CILIUM_FORWARD -o lxc+ -m comment --comment "cilium: any->cluster on lxc+ forward accept" -j ACCEPT
-A CILIUM_FORWARD -i lxc+ -m comment --comment "cilium: cluster->any on lxc+ forward accept (nodeport)" -j ACCEPT
-A CILIUM_INPUT -m mark --mark 0x200/0xf00 -m comment --comment "cilium: ACCEPT for proxy traffic" -j ACCEPT
-A CILIUM_OUTPUT -m mark --mark 0xa00/0xfffffeff -m comment --comment "cilium: ACCEPT for proxy return traffic" -j ACCEPT
-A CILIUM_OUTPUT -m mark --mark 0x800/0xe00 -m comment --comment "cilium: ACCEPT for l7 proxy upstream traffic" -j ACCEPT
-A CILIUM_OUTPUT -m mark ! --mark 0xe00/0xf00 -m mark ! --mark 0xd00/0xf00 -m mark ! --mark 0xa00/0xe00 -m mark ! --mark 0x800/0xe00 -m mark ! --mark 0xf00/0xf00 -m comment --comment "cilium: host->any mark as from host" -j MARK --set-xmark 0xc00/0xf00
-A DOCKER-ISOLATION-STAGE-1 -i docker0 ! -o docker0 -j DOCKER-ISOLATION-STAGE-2
-A DOCKER-ISOLATION-STAGE-1 -j RETURN
-A DOCKER-ISOLATION-STAGE-2 -o docker0 -j DROP
-A DOCKER-ISOLATION-STAGE-2 -j RETURN
-A DOCKER-USER -j RETURN
-A KUBE-FIREWALL ! -s 127.0.0.0/8 -d 127.0.0.0/8 -m comment --comment "block incoming localnet connections" -m conntrack ! --ctstate RELATED,ESTABLISHED,DNAT -j DROP



# iptables -S -t nat
-P PREROUTING ACCEPT
-P INPUT ACCEPT
-P OUTPUT ACCEPT
-P POSTROUTING ACCEPT
-N CILIUM_OUTPUT_nat
-N CILIUM_POST_nat
-N CILIUM_PRE_nat
-N DOCKER
-N IP-MASQ
-N KUBE-KUBELET-CANARY
-A PREROUTING -m comment --comment "cilium-feeder: CILIUM_PRE_nat" -j CILIUM_PRE_nat
-A OUTPUT -m comment --comment "cilium-feeder: CILIUM_OUTPUT_nat" -j CILIUM_OUTPUT_nat
-A POSTROUTING -m comment --comment "cilium-feeder: CILIUM_POST_nat" -j CILIUM_POST_nat
-A POSTROUTING -m comment --comment "ip-masq: ensure nat POSTROUTING directs all non-LOCAL destination traffic to our custom IP-MASQ chain" -m addrtype ! --dst-type LOCAL -j IP-MASQ
-A IP-MASQ -d 169.254.0.0/16 -m comment --comment "ip-masq: local traffic is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 10.0.0.0/8 -m comment --comment "ip-masq: RFC 1918 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 172.16.0.0/12 -m comment --comment "ip-masq: RFC 1918 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 192.168.0.0/16 -m comment --comment "ip-masq: RFC 1918 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 240.0.0.0/4 -m comment --comment "ip-masq: RFC 5735 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 192.0.2.0/24 -m comment --comment "ip-masq: RFC 5737 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 198.51.100.0/24 -m comment --comment "ip-masq: RFC 5737 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 203.0.113.0/24 -m comment --comment "ip-masq: RFC 5737 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 100.64.0.0/10 -m comment --comment "ip-masq: RFC 6598 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 198.18.0.0/15 -m comment --comment "ip-masq: RFC 6815 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 192.0.0.0/24 -m comment --comment "ip-masq: RFC 6890 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -d 192.88.99.0/24 -m comment --comment "ip-masq: RFC 7526 reserved range is not subject to MASQUERADE" -j RETURN
-A IP-MASQ -m comment --comment "ip-masq: outbound traffic is subject to MASQUERADE (must be last in chain)" -j MASQUERADE


```
