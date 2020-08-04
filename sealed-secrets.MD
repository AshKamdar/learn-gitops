**Cluster side**

#1 Install SealedSecret CRD, server-side controller into kube-system namespace.

```bash
kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.12.5/controller.yaml
```

#2 Install kubeseal cil exe from releases page

#3 Fetch the certificate that you will use to encrypt your secrets into sealed secrets:

```bash
kubeseal --fetch-cert > mycert.pem
```

#4 Create secret using command

```bash
kubeseal <mysecret.yml --scope cluster-wide --cert mycert.pem --format yaml >mysealedsecret.yml
```

**Links**

https://docs.d2iq.com/ksphere/dispatch/1.2/tutorials/cd_tutorials/storing-dispatch-secrets-with-gitops/
