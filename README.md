# Aula-10-computacao-em-nuvem

Passo 1 
root@controlplane:~$ kubectl get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   12d   v1.37.1
node01         Ready    <none>          12d   v1.37.1
root@controlplane:~$ kubectl create namespace aula10
namespace/aula10 created
root@controlplane:~$ kubectl -n aula10 create deployment web \
error: required flag(s) "image" not set
root@controlplane:~$ kubectl -n aula10 create deployment web \
  --image=nginx:alpine
deployment.apps/web created
root@controlplane:~$ kubectl -n aula10 rollout status deployment/web
deployment "web" successfully rolled out
root@controlplane:~$ kubectl -n aula10 get deployment,pods
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/web   1/1     1            1           39s

NAME                       READY   STATUS    RESTARTS   AGE
pod/web-6bd469df5c-x6bfb   1/1     Running   0          39s


Passo 2
root@controlplane:~$ kubectl -n aula10 expose deployment web \
  --name=web-svc --port=80 --target-port=80
service/web-svc exposed
root@controlplane:~$ kubectl -n aula10 get service web-svc
kubectl -n aula10 get pods -l app=web
NAME      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
web-svc   ClusterIP   10.101.55.244   <none>        80/TCP    12s
NAME                   READY   STATUS    RESTARTS   AGE
web-6bd469df5c-x6bfb   1/1     Running   0          97s

Passo 3
root@controlplane:~$ kubectl -n aula10 run cliente \
  --image=busybox:1.36 --restart=Never \
  --command -- sleep 3600
pod/cliente created
root@controlplane:~$ kubectl -n aula10 wait --for=condition=Ready \
  pod/cliente --timeout=90s
pod/cliente condition met

Passo 4
root@controlplane:~$ kubectl -n aula10 exec cliente -- \
  wget -qO- http://web-svc | head -n 8
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
root@controlplane:~$ kubectl -n aula10 get deployment web
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
web    1/1     1            1           4m2s

Passo 5
root@controlplane:~$ kubectl -n aula10 get deployment web
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
web    1/1     1            1           4m2s
root@controlplane:~$ kubectl -n aula10 scale deployment/web --replicas=3
deployment.apps/web scaled
root@controlplane:~$ kubectl -n aula10 rollout status deployment/web
deployment "web" successfully rolled out
root@controlplane:~$ kubectl -n aula10 get pods -l app=web -o wide
NAME                   READY   STATUS    RESTARTS   AGE     IP              NODE           NOMINATED NODE   READINESS GATES
web-6bd469df5c-7mf6p   1/1     Running   0          2m13s   192.168.1.95    node01         <none>           <none>
web-6bd469df5c-nzq2d   1/1     Running   0          2m13s   192.168.0.173   controlplane   <none>           <none>
web-6bd469df5c-x6bfb   1/1     Running   0          8m33s   192.168.1.163   node01         <none>           <none>


