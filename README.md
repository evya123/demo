# demo-vpc

### Installing:
1. Set aws credentials and name profile as demo (Or remove thr profile section from aws provider)
2. Clone repo and cd to eks-getting-started 
3. run terraform init & apply and check resources 
4. run terraform apply to create:
    * VPC
    * Private and public subnets
    * K8S cluster with 1 node groupe 
Now we have a k8s cluster running on aws with vpc.
Now we need to set kubectl context so we can connect to k8s cluster.
Using aws cli: '''aws --profile <profile_name> eks --region us-east-1 update-kubeconfig --name terraform-eks-demo'''

To deploy jenkins on the cluster, need to install helm (helm v3 does not need init):
1. Create jenkins namespaces
2. run the kustomization.yaml file to create ServiceAccount, ClutserRole and ClusterRoleBinding for jenkins
3. Using helm, run this command (I assume you have the stable repo and can pull charts):
'''helm install jenkins -n jenkins -f <path to jenkins-values.yaml> jenkins/jenkins'''
Now we have jenkins running on k8s cluster and we can deploy the chart using pipeline
**Please notice that I already added plugins to jenkins**
Now after we configured k8s plugin and aws credentials we can deploy the chart with helm
