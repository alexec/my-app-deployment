# Hands-On

### 1. Install Kustomize

```
brew install kustomize
kustomize version
```

### 2. Fork The Example

* Open https://github.com/gitops-workshop/my-app-deployment
* Click “Fork”. 
* Then, open your terminal and run:

```bash
export username=... ;# your Github username
git clone git@github.com:${username}/my-app-deployment.git
cd my-app-deployment
```

Test:

```
kustomize build base
```

Note: The above URL should start with "git@" and you'll need to enter your username.

### 3. Create An Overlay

```bash
mkdir -p overlays/dev
```

```bash
cat > overlays/dev/kustomization.yaml <<EOL
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base
EOL
```

```bash
kustomize build overlays/dev
git add . && git commit -am "add dev overlay"
git push
```

### 4. Change The Name Prefix

```
kustomize edit set nameprefix ${username}-
git diff
kustomize build
git commit -am "set name prefix"
git push
```

### 5. Open Argo CD And Create Your App

* Open https://argo-cd-kubecon.apps.argoproj.io/
* Click "Login Via Github"
* Click "New application"

| Field | Value |
|-------|-------|
| Application name: | `${username}` |
| Project: | `default` |
| Sync policy: | `Manual` |
| Repository: | `https://github.com/${username}/my-app-deployment` |
| Revision: | `HEAD` |
| Path: | `overlays/dev` |
| Cluster: | `https://kubernetes.default.svc` |
| Namespace: | `default` |
  
### 6. Sync Your App

Click "Sync".

### 7. Upgrade Your App

```
kustomize edit set image gitopsworkshop/my-app:v2
git diff
kustomize build
```

```
git commit -am "upgrade to version 2"
git push
```

* Detect git changes: "Refresh"
* Preview Differences: "App Diff"
* Deploy New Version: "Sync"

### 8. Troubleshoot Degraded App

1. Open app
2. Find the red heart
3. Clik on the resource and check each tab

### 9. GitOps Rollback

```
git revert $(git rev-parse HEAD)
git push
```
