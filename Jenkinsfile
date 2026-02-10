pipeline {
  agent any

  environment {
    IMAGE_NAME = "localhost:5000/nginx-demo"
    IMAGE_TAG  = "2"
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
          podman build \
            -f demo/Dockerfile \
            -t ${IMAGE_NAME}:${IMAGE_TAG} \
            demo
        '''
      }
    }

    stage('Push Image') {
      steps {
        echo 'Pushing image to local registry'
        sh '''
          podman push --tls-verify=false localhost:5000/nginx-demo:2
        '''
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        echo 'Deploying to Kubernetes'
        sh '''
          kubectl apply -f demo/k8s-nginx.yaml
        '''
      }
    }
  }

  post {
    success {
      echo '✅ Pipeline completed successfully'
    }
    failure {
      echo '❌ Pipeline failed'
    }
  }
}
