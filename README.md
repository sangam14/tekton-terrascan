# tekton-terrascan


## Introduction 

install tekton cli 
```
brew install tektoncd-cli

```
Create Kind Cluster 

```
brew install kind
kind create cluster --name kind-2
Creating cluster "kind-2" ...
 ✓ Ensuring node image (kindest/node:v1.21.1) 🖼 
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
⢎⡰ Starting control-plane 🕹️ 
```

# Install tekton pipeline 

```
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```



You can monitor the progress of the install by watching the pods in the newly created tekton-pipelines namespace:

```

namespace/tekton-pipelines created
podsecuritypolicy.policy/tekton-pipelines created
clusterrole.rbac.authorization.k8s.io/tekton-pipelines-controller-cluster-access created
clusterrole.rbac.authorization.k8s.io/tekton-pipelines-controller-tenant-access created
clusterrole.rbac.authorization.k8s.io/tekton-pipelines-webhook-cluster-access created
role.rbac.authorization.k8s.io/tekton-pipelines-controller created
role.rbac.authorization.k8s.io/tekton-pipelines-webhook created
role.rbac.authorization.k8s.io/tekton-pipelines-leader-election created
role.rbac.authorization.k8s.io/tekton-pipelines-info created
serviceaccount/tekton-pipelines-controller created
serviceaccount/tekton-pipelines-webhook created
clusterrolebinding.rbac.authorization.k8s.io/tekton-pipelines-controller-cluster-access created
clusterrolebinding.rbac.authorization.k8s.io/tekton-pipelines-controller-tenant-access created
clusterrolebinding.rbac.authorization.k8s.io/tekton-pipelines-webhook-cluster-access created
rolebinding.rbac.authorization.k8s.io/tekton-pipelines-controller created
rolebinding.rbac.authorization.k8s.io/tekton-pipelines-webhook created
rolebinding.rbac.authorization.k8s.io/tekton-pipelines-controller-leaderelection created
rolebinding.rbac.authorization.k8s.io/tekton-pipelines-webhook-leaderelection created
rolebinding.rbac.authorization.k8s.io/tekton-pipelines-info created
customresourcedefinition.apiextensions.k8s.io/clustertasks.tekton.dev created
customresourcedefinition.apiextensions.k8s.io/conditions.tekton.dev created
customresourcedefinition.apiextensions.k8s.io/pipelines.tekton.dev created
customresourcedefinition.apiextensions.k8s.io/pipelineruns.tekton.dev created
customresourcedefinition.apiextensions.k8s.io/pipelineresources.tekton.dev created
customresourcedefinition.apiextensions.k8s.io/runs.tekton.dev created
customresourcedefinition.apiextensions.k8s.io/tasks.tekton.dev created
customresourcedefinition.apiextensions.k8s.io/taskruns.tekton.dev created
secret/webhook-certs created
validatingwebhookconfiguration.admissionregistration.k8s.io/validation.webhook.pipeline.tekton.dev created
mutatingwebhookconfiguration.admissionregistration.k8s.io/webhook.pipeline.tekton.dev created
validatingwebhookconfiguration.admissionregistration.k8s.io/config.webhook.pipeline.tekton.dev created
clusterrole.rbac.authorization.k8s.io/tekton-aggregate-edit created
clusterrole.rbac.authorization.k8s.io/tekton-aggregate-view created
configmap/config-artifact-bucket created
configmap/config-artifact-pvc created
configmap/config-defaults created
configmap/feature-flags created
configmap/pipelines-info created
configmap/config-leader-election created
configmap/config-logging created
configmap/config-observability created
configmap/config-registry-cert created
deployment.apps/tekton-pipelines-controller created
service/tekton-pipelines-controller created
horizontalpodautoscaler.autoscaling/tekton-pipelines-webhook created
deployment.apps/tekton-pipelines-webhook created
service/tekton-pipelines-webhook created
➜  tekton-terrascan git:(main) ✗ 


```
# monitor pods in tekton-pipelines namespace
```
kubectl get pods --namespace tekton-pipelines --watch

```

Finally, in a couple of the examples, you’ll be pushing container images to Docker Hub. Create a secret that Tekton can use to log in to Docker Hub. Substitute the placeholder values with your own:

```
kubectl create secret docker-registry dockercreds --docker-server=https://index.docker.io/v1/ --docker-username=<DOCKERHUB_USERNAME> --docker-password=<DOCKERHUB_PASSWORD> --docker-email <DOCKERHUB_EMAIL>
```

# terrascan teketon pipeline task 

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: terrascan-pipeline-task
spec:
  steps:
    - name: terrascan-pipeline-step
      image: accurics/terrascan:latest
      command: ["/bin/sh","-c"]
      args:
       - >
        /go/bin/terrascan scan -r git -u https://github.com/sangam14/terrascan-argocd.git -i k8s -t k8s 

```

# start tekton pipeline task 

```
➜  tekton-terrascan git:(main) ✗ tkn task start terrascan-pipeline-task                                   
TaskRun started: terrascan-pipeline-task-run-2rrkc

In order to track the TaskRun progress run:
tkn taskrun logs terrascan-pipeline-task-run-2rrkc -f -n tekton-pipelines
➜  tekton-terrascan git:(main) ✗ tkn taskrun logs terrascan-pipeline-task-run-2rrkc -f -n tekton-pipelines
[terrascan-pipeline-step] 
[terrascan-pipeline-step] 
[terrascan-pipeline-step] Violation Details -
[terrascan-pipeline-step]     
[terrascan-pipeline-step]       Description    :        Containers Should Not Run with AllowPrivilegeEscalation
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        HIGH
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Containers Should Not Run with AllowPrivilegeEscalation
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        HIGH
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Container images with readOnlyRootFileSystem set as false mounts the container root file system with write permissions
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Container images with readOnlyRootFileSystem set as false mounts the container root file system with write permissions
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Default seccomp profile not enabled will make the container to make non-essential system calls
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Default seccomp profile not enabled will make the container to make non-essential system calls
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        No liveness probe will ensure there is no recovery in case of unexpected errors
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        LOW
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        CPU Request Not Set in config file.
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        Medium
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        CPU Request Not Set in config file.
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        Medium
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        CPU Limits Not Set in config file.
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        Medium
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        CPU Limits Not Set in config file.
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        Medium
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        No tag or container image with :Latest tag makes difficult to rollback and track
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        LOW
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        AppArmor profile not set to default or custom profile will make the container vulnerable to kernel level threats
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Image without digest affects the integrity principle of image security
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Image without digest affects the integrity principle of image security
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Memory Request Not Set in config file.
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        Medium
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Memory Request Not Set in config file.
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        Medium
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Memory Limits Not Set in config file.
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        Medium
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Memory Limits Not Set in config file.
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        Medium
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Minimize Admission of Root Containers
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        HIGH
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Minimize Admission of Root Containers
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        HIGH
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Apply Security Context to Your Pods and Containers
[terrascan-pipeline-step]       File           :        k8s-deployment.yml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        Apply Security Context to Your Pods and Containers
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        MEDIUM
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       Description    :        No readiness probe will affect automatic recovery in case of unexpected errors
[terrascan-pipeline-step]       File           :        terrascan-pre-hook.yaml
[terrascan-pipeline-step]       Line           :        1
[terrascan-pipeline-step]       Severity       :        LOW
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       -----------------------------------------------------------------------
[terrascan-pipeline-step] 
[terrascan-pipeline-step] 
[terrascan-pipeline-step] Scan Summary -
[terrascan-pipeline-step] 
[terrascan-pipeline-step]       File/Folder         :   https://github.com/sangam14/terrascan-argocd.git
[terrascan-pipeline-step]       IaC Type            :   k8s
[terrascan-pipeline-step]       Scanned At          :   2021-11-08 17:51:35.995151053 +0000 UTC
[terrascan-pipeline-step]       Policies Validated  :   43
[terrascan-pipeline-step]       Violated Policies   :   24
[terrascan-pipeline-step]       Low                 :   3
[terrascan-pipeline-step]       Medium              :   17
[terrascan-pipeline-step]       High                :   4
[terrascan-pipeline-step] 
[terrascan-pipeline-step] 

➜  tekton-terrascan git:(main) ✗ 

```

What Next ?

Vscode Extension Game Terrascan + Tektonpipline