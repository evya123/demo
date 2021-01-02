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
        sh '''#!/bin/bash
        apt update\
        apt install software-properties-common\
        apt-add-repository --yes --update ppa:ansible/ansible\
        apt install ansible'''
      }
    }
  }
}
