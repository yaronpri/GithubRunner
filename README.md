# GithubRunner

## Instructions
You can find the complete instructions here:
https://github.com/actions-runner-controller/actions-runner-controller/blob/master/docs/detailed-docs.md

In this repo I summarized what action I took in order to make Action-Runner-Controller for GitHub to work on Azure Kubernetes Service.

Before starting installing the GitHub runner, need to have cert-manager installed on the cluster:
https://cert-manager.io/docs/installation/kubectl/ 
(the yaml in this repo is "v1.10.0)
``` 
kubectl apply -f cert-manager.yaml 
```

Install runner controller  (the yaml in this repo is "v0.25.2"v !! change it if needed: https://github.com/actions-runner-controller/actions-runner-controller/releases)

``` 
kubectl create -f actions-runner-controller.yaml 
```

In this example we will show the use of PAT authentication (2nd option is App Authentication), the way to generate GITHUB_TOKEN is explained in the above complete insturctions 
```
kubectl create secret generic controller-manager  -n actions-runner-system --from-literal=github_token=${GITHUB_TOKEN}
```

Install the self-hosted runners on the cluster
```
kubectl apply -f runnerdeployment.yaml 
```

Install the auto-scaler for the runners, in this example we will use the metric ```TotalNumberOfQueuedAndInProgressWorkflowRuns``` which auto-scale based on the lag of the job queue waiting to be processed (maximum pods was set to 20)
```
kubectl apply -f hra-scaler.yaml
```