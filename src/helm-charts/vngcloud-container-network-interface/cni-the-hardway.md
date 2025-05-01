# CNI the hardway

- Deploy k8s cluster from here: ______________________. Start from here

    ```bash
    NAMESPACE     NAME                                    READY   STATUS    RESTARTS        AGE
    kube-system   coredns-76f75df574-brkr2                0/1     Pending   0               6d1h
    kube-system   coredns-76f75df574-x6m58                0/1     Pending   0               6d1h
    kube-system   etcd-control-plane                      1/1     Running   1 (3m26s ago)   6d1h
    kube-system   kube-apiserver-control-plane            1/1     Running   1 (3m26s ago)   6d1h
    kube-system   kube-controller-manager-control-plane   1/1     Running   1 (3m26s ago)   6d1h
    kube-system   kube-proxy-dj567                        1/1     Running   1 (2m3s ago)    6d
    kube-system   kube-proxy-slkzx                        1/1     Running   1 (3m26s ago)   6d1h
    kube-system   kube-proxy-w9stk                        1/1     Running   1 (2m43s ago)   6d
    kube-system   kube-scheduler-control-plane            1/1     Running   1 (3m26s ago)   6d1h
    ```

```bash
k get node control-plane -oyaml | grep podCIDR:
#  podCIDR: 10.244.0.0/24

curl -L https://raw.githubusercontent.com/asayah/ebpf-cni-from-scratch-kubecon-na-2023/main/labs/cni/step3-del -o ebpfcni

cat <<EOT >> 10-ebpfcni.conf
{
    "cniVersion":"1.0.0",
    "name":"ebpfcni",
    "type":"ebpfcni",
    "podcidr":"10.244.0.0/24"
}
EOT


sudo apt install bridge-utils

```
