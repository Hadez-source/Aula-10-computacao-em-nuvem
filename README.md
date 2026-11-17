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
root@controlplane:~$ 
