# Demo of Tekton Operator for cdCon 2021

## Prerequisites:

- Install smee.
- Configure the wekhook URl for the Github repository to send events.

- Install and verify nginx.
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.45.0/deploy/static/provider/cloud/deploy.yaml
```

```aidl
kubectl get pods -n ingress-nginx \
  -l app.kubernetes.io/name=ingress-nginx --watch
```

## Tekton installation

Tekton Operator installation of a previous version 0.21:
```
kubectl apply -f https://storage.googleapis.com/tekton-releases/operator/previous/v0.21.0-1/release.yaml
```

Check the status of Operator:
```
kubectl get deploy -n tekton-operator
```

Installation of Tekton Pipeline, Trigger and Dashboard with the custom resources:
```
kubectl apply -f https://raw.githubusercontent.com/tektoncd/operator/main/config/crs/kubernetes/config/all/operator_v1alpha1_config_cr.yaml
```

Check the log of the Operator:
```
kubectl logs -f deploy/tekton-operator -n tekton-operator
```

Check the status of all Tekton components:
```
kubectl get TektonPipeline,TektonTrigger,TektonDashboard
```

After installing dashboard, install the ingress:
```
kubectl apply -f ingress.yaml
```

Visit http://localhost/dashboard for the dashboard portal.

Dashboard @v0.14.0, Trigger @v0.11.2 and Pipeline @v0.21.0.

## Deployment restore

Pick up a deployment resource of the pipeline, e.g. tekton-pipelines-controller.

Watch all the deployments:
```
kubectl get deploy -n tekton-pipelines -w
```

Remove this deployment resource with command:
```
kubectl delete deploy tekton-pipelines-controller -n tekton-pipelines
```

We expect to see the deployment resource is gone and relaunched.

## Example with Github repository

The event of a newly opened PR will call a TaskRun in the pipeline, via the trigger.

Install the event listener.
```
kubectl apply -f example/github/
```

Connent the webhook url to local event listener service.
```
export WEBHOOK_URL=https://smee.io/pUUFF1sfFnIXhvX
smee -u $WEBHOOK_URL --target http://localhost/ci/
```

