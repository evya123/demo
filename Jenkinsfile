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
      echo "hello world"
    }
  }
}
