pipeline {
  agent {
    kubernetes {
      //cloud 'kubernetes'
      containerTemplate {
        name 'demo'
        image 'alpine:3.7'
        ttyEnabled true
        command 'cat'
      }
    }
  }
  stages {
    stage('demo'){
      steps {
        sh "hello world"
      }
    }
  }
}
