# BBQ ~ Big Bang Quickdev

<img src="https://repo1.dso.mil/gmiernicki/bbq/-/raw/main/assets/bbq192.png">
  
#  

# Installation

### Clone repo to ~/bbq
```
cd ~ && git clone https://repo1.dso.mil/gmiernicki/bbq.git
```
### Add Iron Bank credentials, BBQ kube-config, BBQ path, and some useful aliases to your to ~/.bashrc
```
export REGISTRY1_USERNAME=my-username
export REGISTRY1_PASSWORD=my-password
export KUBECONFIG=~/.kube/bbq-config
export PATH="$PATH:~/bbq/scripts"
. aliases
```
### Add Iron Bank authentication credentials here too
```
cp ~/bbq/overrides/ib_creds.yaml-example ~/bbq/overrides/ib_creds.yaml && \
vi ~/bbq/overrides/ib_creds.yaml
```
### Symlink to your preferred kube config
```
ln -s ~/.kube/greg.miernicki-dev-config ~/.kube/bbq-config
```
# Scripts

These scripts take a small chunk out of the work of spinning up a cluster, deploying resources, debugging helm, and tearing down BigBang. When performing these steps repeatedly, time is saved. It is assumed you have cloned [this repo](https://repo1.dso.mil/platform-one/big-bang/bigbang) to `~/bigbang`.
  
## General Daily Workflow  
  
Spin up a cluster  
```
0-k3d
```
  
Deploy flux  
```
1-flux
```

Deploy BigBang  
```
2-bb
```
  
...dev work / change / break / fix things...  
```  
2-bb
```

Deploy a specific local package  
```
3-package
```

Render a helm template to text file for debugging  
```
4-helm-debug
```
  
...work complete; empty the cluster; next task...
```
8-teardown-bb
```
  
Verify a deployment with a clean install.  
  
```
9-del-ec2 && 0-k3d && 1-flux && 2-bb
```

The k3d dev scripts will self terminate the instantiated ec2 VM 8 hours after initial creation or you can save a few AWS 💰 by executing `9-del-ec2` when you're finished with your cluster.  
  
# Some other scripts

### [aliases](scripts/aliases) 
Some useful shortcuts if you live in the terminal everyday.

### [f](scripts/f) `search-term` 
Recursively non-case-sensitively search for file/dir using a `search-term` hiding all stderr msgs.

### [g](scripts/g) `search-term` 
Recursively search the contents of all files in the pwd for a `search-term`.

### [gitt](scripts/gitt) `commmit msg here`
`git` add . && `git` commit with the commit msg parameter && and `git` push in a single command; AMAZING.    
  
### [reflux](scripts/reflux) `namespace` 
Pause and then resume flux for a given namespace to speed up the rollout of a previous `helm upgrade` command.  

### [kw](scripts/kw) 
Watch pods, helm releases, and git repos used in flux as during an upgrade.  

 
# Overrides

The [overrides](overrides) can be used to deploy a particular set of packages and are typically hand edited into `2-bb`.
  

#  
...bon voyage! ⛵
