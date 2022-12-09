pipeline {
  agent none

  stages {
    stage('Checkout') {
      agent any
      steps {
        git branch: 'main', url: 'https://github.com/heezoochoi/docker-test.git'
      }
    }
    stage('Build') {
      agent {
        docker { image 'maven:3-openjdk-8' }
      }
      steps {
        sh 'mvn clean package -DskipTests=true'
      }
    }
    stage('Test') {
      agent {
        docker { image 'maven:3-openjdk-8' }
      }
      steps {
        sh 'mvn test'
      }
    }
    stage('Build Docker Image') {
      agent any
      steps {
        sh 'docker image build -t heezoochoi/hello:latest .'
      }
    }
    stage('Tag Docker Image') {
      agent any
      steps {
        sh 'docker image tag heezoochoi:$BUILD_NUMBER'
      }
    }
    stage('Publish Docker Image') {
      agent any
      steps {
        withDockerRegistry(credentialsId: 'docker-hub-token', url: 'https://index.docker.io/v1/') {
          sh 'docker image push heezoochoi:$BUILD_NUMBER'
          sh 'docker image push heezoochoi:latest'
        }
      }
    }
    stage('Run Docker Container') {
      agent {
        docker { image 'docker:dind' }
      }
      steps {
        sh 'docker -H tcp://192.168.56.110:2375 container run --detach --name webserver -p 80:8080 heezoochoi/hello:$BUILD_NUMBER'
      }
    }
  }
}

