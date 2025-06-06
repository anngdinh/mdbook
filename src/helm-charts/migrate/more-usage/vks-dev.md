# Migrate VKS-dev

kubectl delete ns $(kubectl get namespaces --no-headers=true -o custom-columns='NAMESPACE:.metadata.name' | grep -v -e 'default' -e velero -e kube- | tr '\n' ' ')
NS=`kubectl get ns |grep Terminating | awk 'NR==1 {print $1}'` && kubectl get namespace "$NS" -o json   | tr -d "\n" | sed "s/\"finalizers\": \[[^]]\+\]/\"finalizers\": []/"   | kubectl replace --raw /api/v1/namespaces/$NS/finalize -f -
verify tat ca node agent deu chay

## Both cluster

```bash
velero install \
    --provider aws \
    --plugins velero/velero-plugin-for-aws:v1.9.0 \
    --use-node-agent \
    --use-volume-snapshots=false \
    --secret-file ./credentials-velero \
    --bucket vks-dev \
    --backup-location-config region=hcm03,s3ForcePathStyle="true",s3Url=https://hcm03.vstorage.vngcloud.vn \
    --default-volumes-to-fs-backup
```

## Source cluster

```bash
velero backup create first --exclude-namespaces velero \
  --include-cluster-resources=true

velero schedule create backup-every-day --schedule "0 0 * * *" \
  --exclude-namespaces kube-public,kube-node-lease,velero,default \
  --include-cluster-resources=true
```

## Target cluster

Create a new cluster in the same subnet with 3 node at least (enable auto-scale).
Label a node with label `kubectl label nodes __________________________________ workload=true`
Apply new file config first, restore will not overwrite: (adjust redis, zone)

```yaml
apiVersion: v1
data:
  vcontainer-ccm.conf: W0dsb2JhbF0KaWRlbnRpdHktdXJsID0gImh0dHBzOi8vaWFtYXBpcy52bmdjbG91ZC52bi9hY2NvdW50cy1hcGkiCnZzZXJ2ZXItdXJsID0gImh0dHBzOi8vaGNtLTMuYXBpLnZuZ2Nsb3VkLnZuL3ZzZXJ2ZXIiCmNsaWVudC1pZCA9ICIxYWI4MWQzNC04NjYzLTRjYzgtOWE5Yy0wOTZkZjVkOTEyODkiCmNsaWVudC1zZWNyZXQgPSAiM2VkMzQ1ZWEtMDE2Ny00YjdhLWI3ZjEtYjY2Y2QyOTAyNWJiIgphbGVydC1jaGFubmVsID0gInRlbGVncmFtOi8vNjA1MTg1MDA4MDpBQUY0YkNBVDNYSVZTak5ONUx4R2hvcFFaWFlFc1Fqc0tIQUAtNDExOTk3NDI0OCIKYWxlcnQtY2hhbm5lbC1zaXplID0gIjEwMCIKY2FjaGUtdXJpID0gInJlZGlzK3NlbnRpbmVsOi8vOkg4djI0Qm4ya2JzeUB2bmdjbG91ZC1jYXBpLXJlZGlzLnJlZGlzLXN5c3RlbToyNjM3OS8xMCIKCltWTEJdCmRlZmF1bHQtbDQtcGFja2FnZS1pZCA9ICJsYnAtOTZiNmIwNzItYWFkYi00YjU4LTlkNWYtYzE2YWQ2OWQzNmFhIgpkZWZhdWx0LXN1Ym5ldC1pZCA9ICJzdWItNDAzYjM2ZDItMzlmYy00N2M0LWI0MGItOGRmMGVjYjcxMDQ1IgpkZWZhdWx0LWxpc3RlbmVyLWFsbG93ZWQtY2lkcnMgPSAie1wiY2lkcnNcIjogW3tcImlwLWFkZHJlc3NcIjpcIjAuMC4wLjBcIixcInN1Ym5ldC1tYXNrXCI6XCIwXCJ9XX0iCmRlZmF1bHQtaWRsZS10aW1lb3V0LWNsaWVudCA9ICI1MCIKZGVmYXVsdC1pZGxlLXRpbWVvdXQtbWVtYmVyID0gIjUwIgpkZWZhdWx0LWlkbGUtdGltZW91dC1jb25uZWN0aW9uID0gIjUiCmRlZmF1bHQtcG9vbC1hbGdvcml0aG0gPSAicm91bmQtcm9iaW4iCmRlZmF1bHQtbW9uaXRvci1oZWFsdGh5LXRocmVzaG9sZCA9ICIzIgpkZWZhdWx0LW1vbml0b3ItdW5oZWFsdGh5LXRocmVzaG9sZCA9ICIzIgpkZWZhdWx0LW1vbml0b3ItdGltZW91dCA9ICI1IgpkZWZhdWx0LW1vbml0b3ItaW50ZXJ2YWwgPSAiNTAiCmRlZmF1bHQtbW9uaXRvci1odHRwLW1ldGhvZCA9ICJnZXQiCmRlZmF1bHQtbW9uaXRvci1odHRwLXBhdGggPSAiLyIKZGVmYXVsdC1tb25pdG9yLWh0dHAtc3VjY2Vzcy1jb2RlID0gIjIwMCIKZGVmYXVsdC1tb25pdG9yLWh0dHAtdmVyc2lvbiA9ICIxLjAiCmRlZmF1bHQtbW9uaXRvci1odHRwLWRvbWFpbi1uYW1lID0gIiIKZGVmYXVsdC1tb25pdG9yLXByb3RvY29sID0gInRjcCIKZGVmYXVsdC1ub2RlLWxhYmVsLXNlbGVjdG9yID0gIndvcmtsb2FkPXRydWUiCnN5cy16b25lLW5hbWUgPSAiYW5uZDJhIgoKW0xvYWRCYWxhbmNlclBvb2xdCmVuYWJsZSA9ICJ0cnVlIgp3b3JrZXItYW1vdW50ID0gIjUiCmxvb3AtaW50ZXJ2YWwgPSAiMzAiCnBvb2wtYnVmZmVyID0gIjEwMCIKc2VydmljZS1wb3J0cyA9ICJ7XCJzZXJ2aWNlLXBvcnRzXCI6IFt7XCJwb29sLW5hbWVcIjpcIndvcmtsb2FkLWNsdXN0ZXJzXCIsXCJwb29sLXNpemVcIjozLFwiaXRlbXNcIjpbe1wibmFtZVwiOlwiYXBpLXNlcnZlci10Y3AtNjQ0M1wiLFwicG9ydFwiOjY0NDMsXCJwcm90b2NvbFwiOlwidGNwXCJ9LHtcIm5hbWVcIjpcImtvbm5lY3Rpdml0eS10Y3AtODEzMlwiLFwicG9ydFwiOjgxMzIsXCJwcm90b2NvbFwiOlwidGNwXCJ9XX1dfSIKW01ldGFkYXRhXQpjaGFydC12ZXJzaW9uID0gIjEuMS42Ig==
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"vcontainer-ccm.conf":"W0dsb2JhbF0KaWRlbnRpdHktdXJsID0gImh0dHBzOi8vaWFtYXBpcy52bmdjbG91ZC52bi9hY2NvdW50cy1hcGkiCnZzZXJ2ZXItdXJsID0gImh0dHBzOi8vaGNtLTMuYXBpLnZuZ2Nsb3VkLnZuL3ZzZXJ2ZXIiCmNsaWVudC1pZCA9ICIxYWI4MWQzNC04NjYzLTRjYzgtOWE5Yy0wOTZkZjVkOTEyODkiCmNsaWVudC1zZWNyZXQgPSAiM2VkMzQ1ZWEtMDE2Ny00YjdhLWI3ZjEtYjY2Y2QyOTAyNWJiIgphbGVydC1jaGFubmVsID0gInRlbGVncmFtOi8vNjA1MTg1MDA4MDpBQUY0YkNBVDNYSVZTak5ONUx4R2hvcFFaWFlFc1Fqc0tIQUAtNDExOTk3NDI0OCIKYWxlcnQtY2hhbm5lbC1zaXplID0gIjEwMCIKY2FjaGUtdXJpID0gInJlZGlzK3NlbnRpbmVsOi8vOkg4djI0Qm4ya2JzeUB2bmdjbG91ZC1jYXBpLXJlZGlzLnJlZGlzLXN5c3RlbToyNjM3OS8wIgoKW1ZMQl0KZGVmYXVsdC1sNC1wYWNrYWdlLWlkID0gImxicC05NmI2YjA3Mi1hYWRiLTRiNTgtOWQ1Zi1jMTZhZDY5ZDM2YWEiCmRlZmF1bHQtc3VibmV0LWlkID0gInN1Yi00MDNiMzZkMi0zOWZjLTQ3YzQtYjQwYi04ZGYwZWNiNzEwNDUiCmRlZmF1bHQtbGlzdGVuZXItYWxsb3dlZC1jaWRycyA9ICJ7XCJjaWRyc1wiOiBbe1wiaXAtYWRkcmVzc1wiOlwiMC4wLjAuMFwiLFwic3VibmV0LW1hc2tcIjpcIjBcIn1dfSIKZGVmYXVsdC1pZGxlLXRpbWVvdXQtY2xpZW50ID0gIjUwIgpkZWZhdWx0LWlkbGUtdGltZW91dC1tZW1iZXIgPSAiNTAiCmRlZmF1bHQtaWRsZS10aW1lb3V0LWNvbm5lY3Rpb24gPSAiNSIKZGVmYXVsdC1wb29sLWFsZ29yaXRobSA9ICJyb3VuZC1yb2JpbiIKZGVmYXVsdC1tb25pdG9yLWhlYWx0aHktdGhyZXNob2xkID0gIjMiCmRlZmF1bHQtbW9uaXRvci11bmhlYWx0aHktdGhyZXNob2xkID0gIjMiCmRlZmF1bHQtbW9uaXRvci10aW1lb3V0ID0gIjUiCmRlZmF1bHQtbW9uaXRvci1pbnRlcnZhbCA9ICI1MCIKZGVmYXVsdC1tb25pdG9yLWh0dHAtbWV0aG9kID0gImdldCIKZGVmYXVsdC1tb25pdG9yLWh0dHAtcGF0aCA9ICIvIgpkZWZhdWx0LW1vbml0b3ItaHR0cC1zdWNjZXNzLWNvZGUgPSAiMjAwIgpkZWZhdWx0LW1vbml0b3ItaHR0cC12ZXJzaW9uID0gIjEuMCIKZGVmYXVsdC1tb25pdG9yLWh0dHAtZG9tYWluLW5hbWUgPSAiIgpkZWZhdWx0LW1vbml0b3ItcHJvdG9jb2wgPSAidGNwIgpkZWZhdWx0LW5vZGUtbGFiZWwtc2VsZWN0b3IgPSAid29ya2xvYWQ9dHJ1ZSIKc3lzLXpvbmUtbmFtZSA9ICJoY20wM2EiCgpbTG9hZEJhbGFuY2VyUG9vbF0KZW5hYmxlID0gInRydWUiCndvcmtlci1hbW91bnQgPSAiNSIKbG9vcC1pbnRlcnZhbCA9ICIzMCIKcG9vbC1idWZmZXIgPSAiMTAwIgpzZXJ2aWNlLXBvcnRzID0gIntcInNlcnZpY2UtcG9ydHNcIjogW3tcInBvb2wtbmFtZVwiOlwid29ya2xvYWQtY2x1c3RlcnNcIixcInBvb2wtc2l6ZVwiOjMsXCJpdGVtc1wiOlt7XCJuYW1lXCI6XCJhcGktc2VydmVyLXRjcC02NDQzXCIsXCJwb3J0XCI6NjQ0MyxcInByb3RvY29sXCI6XCJ0Y3BcIn0se1wibmFtZVwiOlwia29ubmVjdGl2aXR5LXRjcC04MTMyXCIsXCJwb3J0XCI6ODEzMixcInByb3RvY29sXCI6XCJ0Y3BcIn1dfV19IgpbTWV0YWRhdGFdCmNoYXJ0LXZlcnNpb24gPSAiMS4xLjYi"},"kind":"Secret","metadata":{"annotations":{},"labels":{"app.kubernetes.io/instance":"vngcloud-capi-controller-manager"},"name":"vngcloud-controller-manager-config","namespace":"kube-system"},"type":"Opaque"}
  labels:
    app.kubernetes.io/instance: vngcloud-capi-controller-manager
  name: vngcloud-controller-manager-config
  namespace: kube-system
type: Opaque
```

hostPath -> persistent volume

Co backup kube-system chung voi cac ns khac duoc ko? Ko nen
