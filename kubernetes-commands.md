
###Operations in kubectl

#####Bulk Apply

`kubectl` apply also accepts multiple -f arguments:

> kubectl apply -f https://k8s.io/examples/application/nginx/nginx-svc.yaml -f https://k8s.io/examples/application/nginx/nginx-deployment.yaml

With -f we can specify 
  1. file
  2. folder
  3. url
  

#####Bulk Delete

kubectl can also extract resource names from configuration files in order to perform other operations, in particular to 
delete the same resources you created:

> kubectl delete -f https://k8s.io/examples/application/nginx-app.yaml

  
In case of just two resources we specify both on the command line using the resource/name syntax:
> kubectl delete deployments/my-nginx services/my-nginx-svc

For larger numbers of resources, we can specify the selector (label query) specified using -l or --selector, 
to filter resources by their labels:

> kubectl delete deployment,services -l app=nginx


#####Recursive

If you try to perform bulk operation using -f with following structure 

> kubectl apply -f project/k8s/development
    
    project/k8s/development
    ├── configmap
    │   └── my-configmap.yaml
    ├── deployment
    │   └── my-deployment.yaml
    └── pvc
        └── my-pvc.yaml
    
 on `project/k8s/development` will stop at the first level of the directory, not processing any subdirectories. 
 You would have encountered an 
 `error: you must provide one or more resources by argument or filename (.json|.yaml|.yml|stdin)`

Instead, specify the --recursive or -R flag with the --filename,-f flag as such:
> kubectl apply -f project/k8s/development --recursive

The --recursive flag also works when multiple -f arguments are provided:

#####Updating labels

Sometimes existing pods and other resources need to be relabeled before creating new resources. 
This can be done with `kubectl label`

> kubectl label pods -l app=nginx tier=fe


#####Updating annotations
Sometimes you would want to attach annotations to resources. Annotations are arbitrary non-identifying metadata for 
retrieval by API clients such as tools, libraries, etc. This can be done with `kubectl annotate`. For example:

> kubectl annotate pods my-nginx-v4-9gw19 description='my frontend running nginx'

#####Label Query
This outputs all “app=nginx” pods, with an additional label column of pods’ tier (specified with -L or --label-columns)
> kubectl get pods -l app=nginx -L tier


#####Scaling your application
When load on your application grows or shrinks, it’s easy to scale with kubectl. For instance, to decrease the number of nginx replicas from 3 to 1, do:

> kubectl scale deployment/my-nginx --replicas=1

To have the system automatically choose the number of nginx replicas as needed, ranging from 1 to 3, do:

> kubectl autoscale deployment/my-nginx --min=1 --max=3

other flags with autoscaling 
    1. --cpu-percent=