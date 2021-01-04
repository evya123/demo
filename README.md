# demo-vpc

### Installing:
1. Set aws credentials and name profile as demo (Or remove thr profile section from aws provider).
2. Clone repo and cd to eks-getting-started.
3. run terraform init & apply and check resources .
4. run terraform apply to create:
    * VPC.
    * Private and public subnets.
    * K8S cluster with 1 node groupe .
Now we have a k8s cluster running on aws with vpc.
Now we need to set kubectl context so we can connect to k8s cluster.
Using aws cli: ```aws --profile <profile_name> eks --region us-east-1 update-kubeconfig --name terraform-eks-demo```
** I chose to use helm instead of ansible but provided the files for ansible to run **
To deploy jenkins on the cluster, need to install helm (helm v3 does not need init):
1. Create jenkins namespaces.
2. run the kustomization.yaml file to create ServiceAccount, ClutserRole and ClusterRoleBinding for jenkins.
3. Using helm, run this command (I assume you have the stable repo and can pull charts):
```helm install jenkins -n jenkins -f <path to jenkins-values.yaml> jenkins/jenkins```
Now we have jenkins running on k8s cluster and we can deploy the chart using pipeline.
**Please notice that I already added plugins to jenkins**
Now after we configured k8s plugin and aws credentials we can deploy the chart with helm.

Create new pipeline in jenkins or connect to repository to pull jenkinsfile from github.
I chose to use amz2 as agent image base, but you can change to any image and install the right dep.
```
pipeline {
    agent {
        kubernetes {

            containerTemplate {
                name 'shell'
                image 'amazon/aws-cli'
                command 'sleep'
                args 'infinity'
            }
        }
    }
    stages {
        stage('init') {
            steps {
                sh 'yum install -y openssl'
                sh 'yum install -y git'
                sh 'yum install -y tar'
                sh '/usr/bin/git clone https://github.com/pablorsk/kubernetes-helm-hello-world.git'
            }
        }
        stage('deploy_with_helm') {
            steps {
                sh 'curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3'
                sh 'chmod 700 get_helm.sh'
                sh './get_helm.sh'
                sh 'helm create helloworld-chart'
                sh 'helm repo add stable https://charts.helm.sh/stable'
                sh 'helm repo update'
                sh 'helm install helloworld-chart -f helloworld-chart/values.yaml helloworld-chart'
            }
        }
    }
}
```
For autoscaling we can run kubectl or deploy yaml file:
```kubectl -n jenkins autoscale deployment helloworld-chart --cpu-percent=50 --min=1 --max=10```

```
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
 name: helloworld-as
spec:
 scaleTargetRef:
   apiVersion: apps/v1beta1
   kind: Deployment
   name: helloworld-chart
 minReplicas: 3
 maxReplicas: 5
 targetCPUUtilizationPercentage: 85
 ```


In order for you to connecto to the container you need to run this command or assign the right sg with the public subnet
```
export CONTAINER_PORT=$(kubectl get pod --namespace jenkins $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
export POD_NAME=$(kubectl get pods --namespace jenkins -l "app.kubernetes.io/name=helloworld-chart,app.kubernetes.io/instance=helloworld-chart" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace jenkins port-forward $POD_NAME 8080:$CONTAINER_PORT
connect to localhost:8080
```


## For ARANGODB we will deploy it on the k8s cluster using jenkins.
