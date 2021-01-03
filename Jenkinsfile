// Uses Declarative syntax to run commands inside a container.
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
