# Screenshots
To help review your infrastructure, please include the following screenshots in this directory::

## Deployment Pipeline
* DockerHub showing containers that you have pushed
* GitHub repository’s settings showing your Travis webhook (can be found in Settings - Webhook)
* Travis CI showing a successful build and deploy job

## Kubernetes
* To verify Kubernetes pods are deployed properly
```bash
kubectl get pods
```
* To verify Kubernetes services are properly set up
```bash
kubectl describe services
```
* To verify that you have horizontal scaling set against CPU usage
```bash
kubectl describe hpa
```
* To verify that you have set up logging with a backend application
```bash
kubectl logs {pod_name}

Results as follows:

aluche@DESKTOP-F9O2D4O:/mnt/c/Users/ablaz/PycharmProjects/udagram_microservices/k8s$ kubectl logs udagram-api-feed-b9859946-bj7qt

> udagram-api@2.0.0 prod /usr/src/app
> tsc && node ./www/server.js

Executing (default): CREATE TABLE IF NOT EXISTS "FeedItem" ("id"   SERIAL , "caption" VARCHAR(255), "url" VARCHAR(255), "createdAt" TIMESTAMP WITH TIME ZONE, "updatedAt" TIMESTAMP WITH TIME ZONE, PRIMARY KEY ("id"));
Executing (default): SELECT i.relname AS name, ix.indisprimary AS primary, ix.indisunique AS unique, ix.indkey AS indkey, array_agg(a.attnum) as column_indexes, array_agg(a.attname) AS column_names, pg_get_indexdef(ix.indexrelid) AS definition FROM pg_class t, pg_class i, pg_index ix, pg_attribute a WHERE t.oid = ix.indrelid AND i.oid = ix.indexrelid AND a.attrelid = t.oid AND t.relkind = 'r' and t.relname = 'FeedItem' GROUP BY i.relname, ix.indexrelid, ix.indisprimary, ix.indisunique, ix.indkey ORDER BY i.relname;
server running http://localhost:8100
press CTRL+C to stop server
aluche@DESKTOP-F9O2D4O:/mnt/c/Users/ablaz/PycharmProjects/udagram_microservices/k8s$ kubectl describe svc
Name:                     frontend
Namespace:                default
Labels:                   service=frontend
Annotations:              Selector:  service=frontend
Type:                     LoadBalancer
IP:                       10.100.36.90
LoadBalancer Ingress:     a0dc07295cb684e7ab32d95f9ebac065-632992540.us-west-2.elb.amazonaws.com
Port:                     8100  8100/TCP
TargetPort:               80/TCP
NodePort:                 8100  30602/TCP
Endpoints:                192.168.18.156:80,192.168.63.230:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                Age   From                Message
  ----    ------                ----  ----                -------
  Normal  EnsuringLoadBalancer  53m   service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer   53m   service-controller  Ensured load balancer


Name:              kubernetes
Namespace:         default
Labels:            component=apiserver
                   provider=kubernetes
Annotations:       <none>
Selector:          <none>
Type:              ClusterIP
IP:                10.100.0.1
Port:              https  443/TCP
TargetPort:        443/TCP
Endpoints:         192.168.110.221:443,192.168.190.163:443
Session Affinity:  None
Events:            <none>


Name:                     udagram-api-feed
Namespace:                default
Labels:                   service=udagram-api-feed
Annotations:              Selector:  service=udagram-api-feed
Type:                     NodePort
IP:                       10.100.72.16
Port:                     8080  8080/TCP
TargetPort:               8080/TCP
NodePort:                 8080  30734/TCP
Endpoints:                192.168.4.197:8080,192.168.85.119:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>


Name:                     udagram-api-users
Namespace:                default
Labels:                   service=udagram-api-users
Annotations:              Selector:  service=udagram-api-users
Type:                     NodePort
IP:                       10.100.26.55
Port:                     8080  8080/TCP
TargetPort:               8080/TCP
NodePort:                 8080  31493/TCP
Endpoints:                192.168.83.119:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

aluche@DESKTOP-F9O2D4O:/mnt/c/Users/ablaz/PycharmProjects/udagram_microservices/k8s$ kubectl autoscale deployment frontend --cpu-percent=70 --max=4
horizontalpodautoscaler.autoscaling/frontend autoscaled
aluche@DESKTOP-F9O2D4O:/mnt/c/Users/ablaz/PycharmProjects/udagram_microservices/k8s$ kubectl get hpa
NAME       REFERENCE             TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
frontend   Deployment/frontend   <unknown>/70%   1         4         0          11s
aluche@DESKTOP-F9O2D4O:/mnt/c/Users/ablaz/PycharmProjects/udagram_microservices/k8s$ kubectl describe hpa
Name:                                                  frontend
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Sat, 01 Aug 2020 14:43:28 +0200
Reference:                                             Deployment/frontend
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  <unknown> / 70%
Min replicas:                                          1
Max replicas:                                          4
Deployment pods:                                       2 current / 0 desired
Conditions:
  Type           Status  Reason                   Message
  ----           ------  ------                   -------
  AbleToScale    True    SucceededGetScale        the HPA controller was able to get the target's current scale
  ScalingActive  False   FailedGetResourceMetric  the HPA was unable to compute the replica count: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
Events:
  Type     Reason                        Age                From                       Message
  ----     ------                        ----               ----                       -------
  Warning  FailedGetResourceMetric       10s (x3 over 40s)  horizontal-pod-autoscaler  unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
  Warning  FailedComputeMetricsReplicas  10s (x3 over 40s)  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
aluche@DESKTOP-F9O2D4O:/mnt/c/Users/ablaz/PycharmProjects/udagram_microservices/k8s$
  
  
