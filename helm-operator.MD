# GitOps using Helm Opertor

#0 Set Git credentials

```bash
git config user.name "AshKamdar"
git config user.email adventuresincloud@gmail.com
git config user.username AshKamdar

git config --global user.name "AshKamdar"
git config --global user.email adventuresincloud@gmail.com
git config --global user.username AshKamdar
```

If git error, then use following command

```bash
git push -u https://AshKamdar:N3#@github.com/AshKamdar/learn-gitops.git master
```

#1 Add FluxCD repo to helm

```bash
helm repo add fluxcd https://charts.fluxcd.io
```

#2 Create `fluxcd` namespace:

```
kubectl create ns fluxcd
```

#3 Install `Flux` with url of your Git repo

```bash
helm upgrade -i flux fluxcd/flux --wait --namespace fluxcd --set registry.pollInterval=1m --set git.pollInterval=1m --set git.url=git@github.com:AshKamdar/learn-gitops.git --set git-path=kubernetes/templates --set git-user=AshKamdar --set git-email=adventuresincloud@gmail.com --set syncGarbageCollection.enabled=true
```

#4 Get Flux public key

```bash
fluxctl identity --k8s-fwd-ns fluxcd
```

In order to sync your cluster state with Git you need to copy the public key and
create a **deploy key** with **write access** on your GitHub repository.

#5 Install the `HelmRelease` Kubernetes custom resource definition:

```sh
# kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/1.2.0/deploy/crds.yaml
```

#6 Install Flux Helm Operator with **_Helm v3_** support:

```bash
helm upgrade -i helm-operator fluxcd/helm-operator --wait --namespace fluxcd --set git.ssh.secretName=flux-git-deploy --set helm.versions=v3
```

#

NAME: helm-operator
LAST DEPLOYED: Fri Jul 31 23:33:22 2020
NAMESPACE: fluxcd
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Flux Helm Operator docs https://docs.fluxcd.io

Example:

AUTH_VALUES=\$(cat <<-END
usePassword: true
password: "redis_pass"
usePasswordFile: true
END
)

```
kubectl create secret generic redis-auth --from-literal=values.yaml="\$AUTH_VALUES"
```

```
cat <<EOF | kubectl apply -f -
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
name: redis
namespace: default
spec:
releaseName: redis
chart:
repository: https://kubernetes-charts.storage.googleapis.com
name: redis
version: 10.5.7
valuesFrom:

- secretKeyRef:
  name: redis-auth
  values:
  master:
  persistence:
  enabled: false
  volumePermissions:
  enabled: true
  metrics:
  enabled: true
  cluster:
  enabled: false
  EOF
```

watch kubectl get hr

# PodInfo Helm Charts

Get the application URL by running these commands:

```
export POD_NAME=$(kubectl get pods --namespace default -l "app=podinfo,release=podinfo" -o jsonpath="{.items[0].metadata.name}")

echo "Visit http://127.0.0.1:8080 to use your application"

kubectl port-forward podinfo-6696cd4c88-v2dzb 8080:9898
```

Now open browser and visit url

`http://127.0.0.1:8080/`
