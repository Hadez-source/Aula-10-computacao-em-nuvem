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

Passo 6
root@controlplane:~$ kubectl -n aula10 get pods -l app=web -o wide
NAME                   READY   STATUS    RESTARTS   AGE     IP              NODE           NOMINATED NODE   READINESS GATES
web-6bd469df5c-7mf6p   1/1     Running   0          2m13s   192.168.1.95    node01         <none>           <none>
web-6bd469df5c-nzq2d   1/1     Running   0          2m13s   192.168.0.173   controlplane   <none>           <none>
web-6bd469df5c-x6bfb   1/1     Running   0          8m33s   192.168.1.163   node01         <none>           <none>
root@controlplane:~$ kubectl -n aula10 get service web-svc
NAME      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
web-svc   ClusterIP   10.101.55.244   <none>        80/TCP    7m59s
root@controlplane:~$ kubectl -n aula10 get endpoints web-svc
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME      ENDPOINTS                                           AGE
web-svc   192.168.0.173:80,192.168.1.163:80,192.168.1.95:80   8m11s
root@controlplane:~$ kubectl -n aula10 exec cliente -- \
> wget -qO- http://web-svc | head -n 8
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }

Passo 7
root@controlplane:~$ POD=$(kubectl -n aula10 get pods -l app=web \
  -o jsonpath='{.items[0].metadata.name}')
root@controlplane:~$ kubectl -n aula10 delete pod "$POD"
kubectl -n aula10 get pods -l app=web -w
pod "web-6bd469df5c-7mf6p" deleted from aula10 namespace
NAME                   READY   STATUS    RESTARTS   AGE
web-6bd469df5c-nzq2d   1/1     Running   0          4m49s
web-6bd469df5c-v4rjz   1/1     Running   0          1s
web-6bd469df5c-x6bfb   1/1     Running   0          11m

Passo 8
root@controlplane:~$ kubectl -n aula10 scale deployment/web --replicas=2=2
deployment.apps/web scaled
root@controlplane:~$ kubectl -n aula10 rollout status deployment/web
deployment "web" successfully rolled out
root@controlplane:~$ kubectl -n aula10 get deployment,pods
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/web   2/2     2            2           12m

NAME                       READY   STATUS    RESTARTS   AGE
pod/cliente                1/1     Running   0          10m
pod/web-6bd469df5c-nzq2d   1/1     Running   0          6m37s
pod/web-6bd469df5c-x6bfb   1/1     Running   0          12m
root@controlplane:~$ kubectl -n aula10 get endpoints web-svc
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME      ENDPOINTS                           AGE
web-svc   192.168.0.173:80,192.168.1.163:80   11m

Passo 9
Antes   Desejado: 1 réplica.   Pods prontos: 1 pod em execuçao   
Service responde? Sim, o teste de acesso via wget pelo pod cliente retornou a página padrão do Nginx com sucesso

Pico   Desejado: 3 réplicas.   Pods prontos: 3 pods rodando simultaneamente   
Endereços: 3 endereços IP distintos registrados nos endpoints do serviço 192.168.0.173:80, 192.168.1.163:80 e 192.168.1.95:80   

Após falha   Pod apagado: Sim, o pod web-6bd469df5c-7mf6p foi deletado manualmente para simular uma falha.   Reposição: Ocorreu de forma imediata e automática pelo ReplicaSet, que subiu o novo pod  para manter as 3 réplicas desejadas 
Service responde? Sim, mesmo após a remoção de um pod e o reajuste de escala, os endpoints foram atualizados para os 2 IPs restantes (192.168.0.173:80 e 192.168.1.163:80) e o serviço seguiu respondendo sem interrupções.
