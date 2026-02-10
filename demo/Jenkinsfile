pipeline {
  agent any

  environment {
    REGISTRY = "localhost:5000"
    IMAGE    = "nginx-demo"
    TAG      = "${BUILD_NUMBER}"
  }

  stages {

    stage('Checkout') {
      steps {
        echo 'Pulling code from GitHub'
        checkout scm
      }
    }

    stage('Build Image') {
      steps {
        echo 'Building NGINX image with Podman'
        sh '''
          podman build -t $REGISTRY/$IMAGE:$TAG .
        '''
      }
    }

    stage('Push Image') {
      steps {
        echo 'Pushing image to local registry'
        sh '''
          podman push $REGISTRY/$IMAGE:$TAG
        '''
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        echo 'Deploying NGINX to Kubernetes'
        sh '''
          sed -i "s|IMAGE_TAG|$TAG|g" k8s-nginx.yaml
          kubectl apply -f k8s-nginx.yaml
        '''
      }
    }
  }
}
