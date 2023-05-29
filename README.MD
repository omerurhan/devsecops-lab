# DevSecOps Lab Environment

Install Prerequisite Tools on MacOS.
```
# Install brew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Ansible
## Check pip module if does not exit install pip
python3 -m pip -V
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py

python3 get-pip.py --user

## Install ansible
python3 -m pip install --user ansible

ansible --version
```

Create lab environment
```
# For macos
ansible-playbook -i inventory playbooks/lab-macos.yml
# For linux
ansible-playbook -i inventory playbooks/lab-linux.yml
```

# Domains
---
```
jenkins.demo.io

sonarqube.demo.io

vault.demo.io

kibana.demo.io

todos.demo.io

adminer.demo.io

cerebro.demo.io
```


Create todos db and table than add text item column


Create dockerhub secret in jenkins namespace
```
kubectl create secret docker-registry regcred --docker-server=https://index.docker.io/v1/ --docker-username=<username> --docker-password=<password> -n jenkins
```
Create jenkins secret file with kubeconfig.
[jenkins.demo.io](jenkins.demo.io)


```
# Download the talisman installer script
curl https://thoughtworks.github.io/talisman/install.sh > ~/install-talisman.sh
chmod +x ~/install-talisman.sh

# as a pre-push hook
~/install-talisman.sh
# or as a pre-commit hook
~/install-talisman.sh pre-commit
```

precommit app for hook chaninig
pip3 install pre-commit
```
# add .pre-commit-config.yaml

repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
    - id: check-yaml
    - id: end-of-file-fixer
    - id: trailing-whitespace

pre-commit install
```

SAST with Sonarqube

[sonarqube.demo.io](sonarqube.demo.io)

- change password on gui

- create manual project and get sonarscanner text from sonarqube

- add sonarscanner stage to pipeline.

- add qualitygate intergration to pipeline for break pipeline if quality gate conditions are not met.

    - create token for project todos in sonarqube
    - create secret-text for token in jenkins
    - create sonarqube conf with use secret text in jenkins
    - add sonarqube-webhook in sonarqube
    - add qualitygate step to jenkinsfile


- add dependecy check. for go install:

- go install golang.org/x/vuln/cmd/govulncheck@latest  
govulncheck ./...

- trivy scan add to vulnerability scan

- conftest for docker image
https://github.com/gbrindisi/dockerfile-security.git