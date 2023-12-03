pipeline {
  agent any
  environment {
    DOCKER_HUB_CREDENTIALS = 'b43dd413-d663-41de-ab0b-4ab331602aaa'
    DOCKER_IMAGE_NAME = 'kirtighugtyal006/mvn-hello-world'
    DOCKER_IMAGE_TAG = 'latest'
    DOCKER_HOME = tool 'docker'
    PATH = "${DOCKER_HOME}/bin:${env.PATH}"
  }
  stages {
    stage("Build") {
      steps {
        script {
          withMaven(
            maven: 'maven3',
            goals: 'clean install'
          ) {}
        }
      }
    }
    stage("Test") {
      steps {
        script {
          withMaven(
            maven: 'maven3',
            goals: 'test'
          ) {}
        }
      }
    }
    stage("Integration Tests") {
      steps {
        script {
          withMaven(
            maven: 'maven3',
            goals: 'integration-test'
          ) {}
        }
      }
    }
    stage('Build and Push Image to Docker Hub') {
      steps {
        script {
          def dockerImage = docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")
          docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS}") {
            dockerImage.push()
          }
        }
      }
    }
    stage("Deploy") {
      steps {
        withKubeConfig([credentialsId: 'kube-config']) {
          sh 'kubectl apply -f kubernetes/namespace/namespace.yml'
          sh 'kubectl apply -f kubernetes/app/deployment.yml'
          sh 'kubectl apply -f prometheus-config.yaml'
          sh 'kubectl apply -f grafana-config.yaml'
        }
      }
    }
  }
}
