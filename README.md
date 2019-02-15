# activiti-cloud-environments

The default git repository used when creating new Activiti Cloud GitOps based Environments

### How to use

1. Get Jx Cluster Domain:

```
$ export CLUSTER_DOMAIN="$(kubectl get cm ingress-config -o=go-template --template='{{.data.domain}}' -n jx)"
```

2. Set CLUSTER_NAME environment variable for the cluster 

```
$ export CLUSTER_NAME="....."
```

3. Create Activiti Cloud DevOps Environment. 

Run this command to configure environment using domain name of the cluster with the Git repository used as the fork when creating new Environment Git repos
and custom environment repo prefix. Your Environment Git repo will be of the form 'environment-$prefix-$envName'

```
$ jx create env --domain $CLUSTER_DOMAIN \
	--fork-git-repo='https://github.com/activiti/activiti-cloud-environments.git' \
	--prefix=$CLUSTER_NAME
```

4. After Jx creates the environment in your Git repository, open your repo and edit env/values.yaml file 
to set the value of `global.gateway.domain` key from `expose.config.domain` value

```
expose:
  config:
    domain: 1.2.3.4.nip.io # <==== Use this value to set global.gateway.domain configuration

global:
  gateway:
    annotations:
      certmanager.k8s.io/issuer: letsencrypt-prod

    domain: REPLACEME # <=== Set value from expose.config.domain key, i.e. 1.2.3.4.nip.io

    host: activiti-cloud-gateway.{{ .Release.Namespace }}.{{ .Values.global.gateway.domain }}
    http: true

  keycloak:
    client: activiti
    host: ""
    realm: activiti
    resource: activiti
    url: ""
```    
