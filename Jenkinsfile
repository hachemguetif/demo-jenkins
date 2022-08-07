pipeline {
  agent {
    kubernetes {
      label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
      defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
    }
  }
  environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub-cred')
	}
  stages {
    stage('Build') {
      steps {  
        sh "mvn clean install"   
      }
    }
    stage('Build And Push Docker Image') {
      steps {
        container('docker') {  
          sh "docker build -t hachemguetif/jenkins:latest ."  
          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
          sh 'docker push hachemguetif/jenkins:latest'
          sh 'docker logout'       
        }
      }
    }
  }
}