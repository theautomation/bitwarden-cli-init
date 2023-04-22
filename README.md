# bitwarden-cli-init

[![Build Status](https://drone.theautomation.nl/api/badges/theautomation/bitwarden-cli-init/status.svg)](https://drone.theautomation.nl/theautomation/bitwarden-cli-init)
![GitHub repo size](https://img.shields.io/github/repo-size/theautomation/bitwarden-cli-init?logo=Github)
![GitHub commit activity](https://img.shields.io/github/commit-activity/y/theautomation/bitwarden-cli-init?logo=github)
![GitHub last commit (branch)](https://img.shields.io/github/last-commit/theautomation/bitwarden-cli-init/main?logo=github)

<img src="https://github.com/theautomation/kubernetes-gitops/blob/main/assets/img/k8s.png?raw=true" alt="K8s" style="height: 30px; width:30px;"/>
Application running in Kubernetes

This deployment (Kubernets pod) can be used as an init kubernetes container to
pull a secret from a Bitwarden vault and share the secret with the main
container(s) via a file, you can mount this file into the main container(s)

Create a Kubernets secret with the credentials of you Bitwarden/Vaultwarden
instance like this (I am using sealedsecret to encrypt password and other
secrets)

```yaml
kind: SealedSecret
apiVersion: bitnami.com/v1alpha1
metadata:
  name: bitwarden-cli-env-secrets
  namespace: tools
  lables:
    app.kubernetes.io/name: bitwarden-cli
    app.kubernetes.io/component: cli
    app.kubernetes.io/instance: production
    app.kubernetes.io/part-of: vaultwarden
spec:
  encryptedData:
    BW_CLIENTID: "Your bitwarden client ID"
    BW_CLIENTSECRET: "Your bitwarden client secret"
    BW_PASSWORD: "Your bitwarden password"
  template:
    metadata:
      labels:
        app: bitwarden-cli
      name: bitwarden-cli-env-secrets
      namespace: tools
    type: Opaque
```

It pulls by default a 'username', 'password' and 'TOTP' and make them in a file
on this location `/tmp/.retrieved.env`. Use the command
`eval $(cat /tmp/.retrieved.env)` to make the vaiables available as OS
environment variables inside the container(s).

```bash
cat <<EOF >/tmp/.retrieved.env
RETRIEVED_USERNAME="${USERNAME}"
RETRIEVED_PASSWORD="${PASSWORD}"
RETRIEVED_TOTP="${TOTP}"
EOF
```
