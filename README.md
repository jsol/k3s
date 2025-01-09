# Server setup

## TODO
dnsmasq
ufw
loopia update (in k3s)
rootsy
backups
minio buckets & users


## IP

## ufw

## dnsmasq

## K3S
### Env
All the either "change often" or "private" info is stored in env.sh and should
be substituted into the files before applying:

```
source ../env.sh
cat 400-deploy.yaml | envsubst | kubectl apply -f -
```

Store the env file with gpg

  gpg --output env.gpg --symmetric env.sh

Decrypt

  gpg --output env.sh --decrypt env.gpg


### Registry
Create the auth file:
  docker run --entrypoint htpasswd httpd:2 -Bbn "${RETISTRY_USER}" ${REGISTRY_PASS} > auth/htpasswd

Set up the credentials in the k3s file

``` /etc/rancher/k3s/registries.yaml
mirrors:
    ${REGISTRY_FQDN}:
          endpoint:
            - "https://${REGISTRY_FQDN}:443/"

configs:
  ${REGISTRY_FQDN}:
    auth:
      username: ${REGISTRY_USER}
      password: ${REGISTRY_PASS}
    tls:
       insecure_skip_verify: true
```

### DGgraph
Just one node so just on alpha and one zero, so no redundancy. Issues with
resolving the hostname, so routing through the service, which makes scaling
up bad without changing the setup.

### Kubernetes general
Connecting to a pod port
  kubectl port-forward pods/dgraph-zero-db986d89d-nrlzr -n dgraph 8080:8080

Restarting a pod
  kubectl rollout restart deployment <deployment_name> -n <namespace>
