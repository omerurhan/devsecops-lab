# DevSecOps Lab Environment

## Install Prerequisite Tools.
---
```
# Install brew for MacOS
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Ansible for MacOs
# Check pip module
python3 -m pip -V

# if does not exit install pip
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py --user

# Install ansible
python3 -m pip install --user ansible
ansible --version

# Install Ansible for Ubuntu 22.04
sudo apt-add-repository ppa:ansible/ansible
sudo apt update
sudo apt install ansible
```

Create Lab environment with ansible
```
# For macos
ansible-playbook -i inventory playbooks/lab-macos.yml

# For linux
ansible-playbook -i inventory playbooks/lab-linux.yml
```

## Lab Domains
---
```
jenkins.demo.io
sonarqube.demo.io
vault.demo.io
kibana.demo.io
todos.demo.io
adminer.demo.io
cerebro.demo.io
grafana.demo.io
prometheus.demo.io
alertmanager.demo.io
```

## Post installation steps
---
- Create todos db and todos table than add text item column with adminer gui

- Create dockerhub secret in jenkins namespace
```
kubectl create secret docker-registry regcred --docker-server=https://index.docker.io/v1/ --docker-username=<username> --docker-password=<password> -n jenkins
```

- Create jenkins secret-file with kubeconfig with jenkins gui.
[jenkins.demo.io](jenkins.demo.io)


## Lab scenarios
---
- Create DevOps pipeline with jenkins gui

- Download the talisman installer script and configure for local repository.
```
curl https://thoughtworks.github.io/talisman/install.sh > ~/install-talisman.sh
chmod +x ~/install-talisman.sh

# as a pre-push hook
~/install-talisman.sh
# or as a pre-commit hook
~/install-talisman.sh pre-commit
```

- precommit app for hook chaninig

```
# install pre-commit
pip3 install pre-commit

# add .pre-commit-config.yaml file to repo

repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
    - id: check-yaml
    - id: end-of-file-fixer
    - id: trailing-whitespace

pre-commit install
```

- SAST with Sonarqube
[sonarqube.demo.io](sonarqube.demo.io)

    - change password on gui

    - create manual project and get sonarscanner text from sonarqube for go app.

    - add sonarscanner stage to pipeline.

    - add qualitygate integration to pipeline for break pipeline if quality gate conditions are not met.

    - create token for project in sonarqube
    - create secret-text with token in jenkins
    - create sonarqube conf with secret-text in jenkins
    - add sonarqube-webhook on sonarqube
    - add qualitygate step to jenkinsfile


- Add dependecy check for go app

```
go install golang.org/x/vuln/cmd/govulncheck@latest  
govulncheck ./...
```
- trivy scan add for vulnerability scan

- OPA conftest for docker image
https://github.com/gbrindisi/dockerfile-security.git

- Add OPA conftest for kubernetes

- Configure deployment with rollback options

- Add integration test

- DAST with OWASP ZAP DAST

    - Configure pipeline stage
    - Generate HTLM report and upload to jenkins
    - Ignore warning and pass pipeline

## Hashicorp Vault Configuration
---
```
# Unseal vault
vault operator init
vault operator unseal

export VAULT_TOKEN="root-token"

# enable kv secret 
vault secrets enable -path=crds kv-v2

# add sensitive data to kv store
vault kv put crds/psql username=postgres password=password

vault kv get crds/psql 
vault kv get database/username

# Create policy for todos app
cat <<EOF > /home/vault/app-policy.hcl
path "crds/data/psql" { 
   capabilities = ["read"]
}
EOF

vault policy write todos /home/vault/app-policy.hcl

vault policy list
vault policy read todos


# Enable kubernetes auth
vault auth enable kubernetes

vault write auth/kubernetes/config \
   token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
   kubernetes_host=https://${KUBERNETES_PORT_443_TCP_ADDR}:443 \
   kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt

vault write auth/kubernetes/role/todos \
   bound_service_account_names=todos \
   bound_service_account_namespaces=dev \
   policies=todos \
   ttl=3h

k get clusterrolebindings vault-server-binding


# Update k8s deploy manifest to use vault secret.
```