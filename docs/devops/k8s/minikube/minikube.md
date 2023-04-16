## minikube
```shell
# 1.Installation
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube

# 2.Start your cluster
minikube start

# 3.Interact with your cluster
kubectl get po -A # or: minikube kubectl -- get po -A

# bring-up dashboard
minikube dashboard
```

## krew
```shell
# 1.Make sure that git is installed.

# 2.Run this command to download and install krew:
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)

# 3.Add the $HOME/.krew/bin directory to your PATH environment variable. To do this, update your .bashrc or .zshrc file and append the following line:
# and restart your shell.
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH" 

# 4.Run kubectl krew to check the installation.
```

## node-shell
`kubectl krew install node-shell`

## Reference
* [minikube start](https://minikube.sigs.k8s.io/docs/start/)
* [krew plugin install](https://krew.sigs.k8s.io/docs/user-guide/setup/install/)