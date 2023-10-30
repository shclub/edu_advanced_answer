# 과제

<br/>

## 과제 1. VM 에 Node Exporter 설치 하고 Prometheus로 수집하기  


<br/>

참고  
- https://www.justinpolidori.it/posts/20210829_monitor_external_services_with_promethues_outside_kubernetes/  
- https://velog.io/@sojukang/%EC%84%B8%EC%83%81%EC%97%90%EC%84%9C-%EC%A0%9C%EC%9D%BC-%EC%89%AC%EC%9A%B4-Prometheus-Grafana-%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81-%EC%84%A4%EC%A0%95  

<br/>

1. vm 서버에 접속하여 node_exporter 를 다운 받는다.   
- https://github.com/prometheus/node_exporter/releases

<br/>

```bash
root@edu25:~# wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
--2023-10-24 09:43:49--  https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
Resolving github.com (github.com)... 20.200.245.247
Connecting to github.com (github.com)|20.200.245.247|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/9524057/5509b569-5c34-471e-8598-c05c0733bb7f?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231024%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231024T094350Z&X-Amz-Expires=300&X-Amz-Signature=cc30793867692725325ab1efbc5716cbc2026fb69286078edd1b9538c1f5356f&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=9524057&response-content-disposition=attachment%3B%20filename%3Dnode_exporter-1.6.1.linux-amd64.tar.gz&response-content-type=application%2Foctet-stream [following]
--2023-10-24 09:43:50--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/9524057/5509b569-5c34-471e-8598-c05c0733bb7f?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231024%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231024T094350Z&X-Amz-Expires=300&X-Amz-Signature=cc30793867692725325ab1efbc5716cbc2026fb69286078edd1b9538c1f5356f&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=9524057&response-content-disposition=attachment%3B%20filename%3Dnode_exporter-1.6.1.linux-amd64.tar.gz&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.111.133, 185.199.108.133, 185.199.109.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.111.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 10368103 (9.9M) [application/octet-stream]
Saving to: ‘node_exporter-1.6.1.linux-amd64.tar.gz’

node_exporter-1.6.1.linux-amd64.tar.gz         100%[=================================================================================================>]   9.89M  --.-KB/s    in 0.04s

2023-10-24 09:43:50 (274 MB/s) - ‘node_exporter-1.6.1.linux-amd64.tar.gz’ saved [10368103/10368103]
```  


<br/>

2. 압축을 풀고 /usr/local/bin 폴더로 실행 화일을 변경하여 이동한다.   

```bash
root@edu25:~# tar xvfz node_exporter-*.*-amd64.tar.gz
root@edu25:~# sudo mv node_exporter-*.*-amd64/node_exporter /usr/local/bin/
```

<br/>


3. node_exporter 서비스를 실행한다.  

```bash
root@edu25:~# node_exporter &
[2] 9419
[1]   Killed                  node_exporter
root@edu25:~# ts=2023-10-24T09:50:40.560Z caller=node_exporter.go:180 level=info msg="Starting node_exporter" version="(version=1.6.1, branch=HEAD, revision=4a1b77600c1873a8233f3ffb55afcedbb63b8d84)"
ts=2023-10-24T09:50:40.560Z caller=node_exporter.go:181 level=info msg="Build context" build_context="(go=go1.20.6, platform=linux/amd64, user=root@586879db11e5, date=20230717-12:10:52, tags=netgo osusergo static_build)"
ts=2023-10-24T09:50:40.560Z caller=node_exporter.go:183 level=warn msg="Node Exporter is running as root user. This exporter is designed to run as unprivileged user, root is not required."
ts=2023-10-24T09:50:40.560Z caller=diskstats_common.go:111 level=info collector=diskstats msg="Parsed flag --collector.diskstats.device-exclude" flag=^(ram|loop|fd|(h|s|v|xv)d[a-z]|nvme\d+n\d+p)\d+$
ts=2023-10-24T09:50:40.561Z caller=filesystem_common.go:111 level=info collector=filesystem msg="Parsed flag --collector.filesystem.mount-points-exclude" flag=^/(dev|proc|run/credentials/.+|sys|var/lib/docker/.+|var/lib/containers/storage/.+)($|/)
ts=2023-10-24T09:50:40.561Z caller=filesystem_common.go:113 level=info collector=filesystem msg="Parsed flag --collector.filesystem.fs-types-exclude" flag=^(autofs|binfmt_misc|bpf|cgroup2?|configfs|debugfs|devpts|devtmpfs|fusectl|hugetlbfs|iso9660|mqueue|nsfs|overlay|proc|procfs|pstore|rpc_pipefs|securityfs|selinuxfs|squashfs|sysfs|tracefs)$
ts=2023-10-24T09:50:40.562Z caller=node_exporter.go:110 level=info msg="Enabled collectors"
...
level=info msg="Listening on" address=[::]:9100
ts=2023-10-24T09:50:40.565Z caller=tls_config.go:277 level=info msg="TLS is disabled." http2=false address=[::]:9100
```  

<br/> 

4. metric 을 조회해 본다.  

```bash
root@edu25:~# curl localhost:9100/metrics
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0# HELP go_gc_duration_seconds A summary of the pause duration of garbage collection cycles.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 0
go_gc_duration_seconds{quantile="0.25"} 0
go_gc_duration_seconds{quantile="0.5"} 0
go_gc_duration_seconds{quantile="0.75"} 0
go_gc_duration_seconds{quantile="1"} 0
go_gc_duration_seconds_sum 0
go_gc_duration_seconds_count 0
# HELP go_goroutines Number of goroutines that currently exist.
# TYPE go_goroutines gauge
go_goroutines 7
# HELP go_info Information about the Go environment.
# TYPE go_info gauge
go_info{version="go1.20.6"} 1
# HELP go_memstats_alloc_bytes Number of bytes allocated and still in use.
# TYPE go_memstats_alloc_bytes gauge
go_memstats_alloc_bytes 851048
# HELP go_memstats_alloc_bytes_total Total number of bytes allocated, even if freed.
# TYPE go_memstats_alloc_bytes_total counter
go_memstats_alloc_bytes_total 851048
# HELP go_memstats_buck_hash_sys_bytes Number of bytes used by the profiling bucket hash table.
# TYPE go_memstats_buck_hash_sys_bytes gauge
go_memstats_buck_hash_sys_bytes 1.446152e+06
# HELP go_memstats_frees_total Total number of frees.
# TYPE go_memstats_frees_total counter
go_memstats_frees_total 725
...
```


<br/> 

5. k8s 에서 서비스를 생성한다.  

```bash
root@edu25:~# cat  external-node-exporter-svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: external-node-exporter
  labels:
    app: external-node-exporter
spec:
  ports:
    - name: metrics
      port: 9100
      protocol: TCP
      targetPort: 9100
root@edu25:~# kubectl apply -f external-node-exporter-svc.yaml
service/external-node-exporter created
root@edu25:~# kubectl get svc
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
external-node-exporter   ClusterIP   172.30.94.58   <none>        9100/TCP   2m
```

<br/>

6. k8s 에서 external 시스템과 연결하기 위한 End Point를 생성한다.     
- ip에는 본인의 vm ip를 넣어준다. 

```bash
root@edu25:~# cat external-node-exporter-ep.yaml
apiVersion: v1
kind: Endpoints
metadata:
  name: external-node-exporter
subsets:
  - addresses:
    - ip: 211.251.238.182
    ports:
      - name: metrics
        port: 9100
        protocol: TCP
root@edu25:~# kubectl apply -f external-node-exporter-ep.yaml
endpoints/external-node-exporter created
root@edu25:~# kubectl get endpoints
NAME                     ENDPOINTS              AGE
external-node-exporter   211.251.238.182:9100   9s
```

<br/>

ServiceMonitor 를 생성하기 위해서는 role를 생성하고 rolebinding을 해야 한다.    

```bash
[root@bastion monitoring]# cat service_monitor_role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: servicemonitor-role
rules:
- apiGroups: ["monitoring.coreos.com"]
  resources: ["servicemonitors"]
  verbs: ["*"]
[root@bastion monitoring]# kubectl apply -f  service_monitor_role.yaml
```  

<br/>

`edu25` 유저에게 권한을 할당한다.  

```bash 
[root@bastion monitoring]# kubectl create rolebinding servicemonitor-rolebinding --role=servicemonitor-role --user=edu25
```  

<br/>

아래와 같이 조회가 가능하다.   


```bash 
root@edu25:~# kubectl get servicemonitor
NAME                     AGE
external-node-exporter   30m
```

<br/>

7. ServiceMonitor 를 생성한다.     
- namespaceSelector에는  본인의 namespace 를 넣어준다. 

<br/>

```bash
root@edu25:~# cat external-node-exporter-sm.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: external-node-exporter
  labels:
    app: external-node-exporter
spec:
  endpoints:
    - port: metrics
      interval: 30s
      path: /metrics
  selector:
    matchLabels:
      app: external-node-exporter
  namespaceSelector:
    matchNames:
      - edu25
root@edu25:~# kubectl apply -f external-node-exporter-sm.yaml
servicemonitor.monitoring.coreos.com/external-node-exporter created
root@edu25:~# kubectl get servicemonitor
NAME                     AGE
external-node-exporter   51s
```
<br/>

Administration -> CustomeResourceDefinitions 로 이동하여 servicemonitor를 검색한다.  

<img src="./assets/service_monitor_1.png" style="width: 80%; height: auto;"/>


<br/>

servicemonitor를 클릭하고 Instances 를 선택하고 난후 external 로 검색을 하면 우리가 생성한 servicemonitor 를 확인 할 수 있다.  

<img src="./assets/service_monitor_2.png" style="width: 80%; height: auto;"/>

<br/>

8. okd 콘솔의 Observe에서 target 이 생성 되었는지 확인한다.    

<br/>

<img src="./assets/service_monitor_3.png" style="width: 80%; height: auto;"/>

<br/>

9. okd 콘솔의 Observe에서 metric를 조회해 본다.    

<br/>

```bash
sum(rate(node_cpu_seconds_total{instance="211.251.238.182:9100"}[1m])/32*100)
```

<br/>

<img src="./assets/service_monitor_4.png" style="width: 80%; height: auto;"/>

<br/>

10. grafana 에 dashboard 를 설치한다.  
- 대쉬보드번호 : 15172

<br/>

job을 external-node-exporter 로 설정하고 refresh 한다.  

<img src="./assets/service_monitor_5.png" style="width: 80%; height: auto;"/>

<br/>

### 추가 :docker/podman 으로 node exporter 설치 하기

<br/>


```bash
[root@bastion monitoring]# podman run  -d -p 9100:9100 prom/node-exporter
✔ docker.io/prom/node-exporter:latest
Trying to pull docker.io/prom/node-exporter:latest...
Getting image source signatures
Copying blob 2b6642e6c59e done
Copying blob d5c4df21b127 done
Copying blob 2f5f7d8898a1 done
Copying config 458e026e6a done
Writing manifest to image destination
Storing signatures
3b79c5a114c3c6908c33539230ba9926d907651824cd419f8660f6537b0618d5
[root@bastion monitoring]# podman ps
CONTAINER ID  IMAGE                                COMMAND     CREATED         STATUS         PORTS                   NAMES
3b79c5a114c3  docker.io/prom/node-exporter:latest              24 seconds ago  Up 24 seconds  0.0.0.0:9100->9100/tcp  stoic_black
```  

```bash
[root@bastion monitoring]# curl http://192.168.1.40:9100/metrics
```

```bash
[root@bastion monitoring]# kubectl apply -f external-node-exporter-svc.yaml -n shclub
service/external-node-exporter created
[root@bastion monitoring]# kubectl apply -f external-node-exporter-ep.yaml -n shclub
endpoints/external-node-exporter created
[root@bastion monitoring]# kubectl apply -f external-service-monitor.yaml -n shclub
servicemonitor.monitoring.coreos.com/external-node-exporter created
```

```bash
[root@bastion monitoring]# kubectl get svc -n shclub  | grep external-node-exporter
external-node-exporter                                ClusterIP   172.30.33.239    <none>        9100/TCP         3m11s
[root@bastion monitoring]# kubectl get servicemonitor -n shclub
NAME                     AGE
external-node-exporter   11m
```

<br/>