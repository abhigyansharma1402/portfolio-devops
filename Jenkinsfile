pipeline {
  agent any
  environment {
    DOCKERHUB_USER = 'abhigyansharma'
    IMAGE_NAME     = "${DOCKERHUB_USER}/portfolio-site"
    BUILD_TAG      = "v${BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps { git branch: 'main', url: 'https://github.com/abhigyansharma1402/portfolio-devops.git' }
    }
    stage('Build Image') {
      steps { sh 'docker build -t $IMAGE_NAME:$BUILD_TAG -t $IMAGE_NAME:latest ./website' }
    }
    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
            usernameVariable: 'DUSER', passwordVariable: 'DPASS')]) {
          sh 'echo $DPASS | docker login -u $DUSER --password-stdin'
          sh 'docker push $IMAGE_NAME:$BUILD_TAG'
          sh 'docker push $IMAGE_NAME:latest'
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl set image deployment/portfolio-deployment portfolio-site=$IMAGE_NAME:$BUILD_TAG --record'
        sh 'kubectl rollout status deployment/portfolio-deployment'
      }
    }
  }
}