pipeline {
  agent any

  environment {
    DOCKERHUB_USER = 'akashpandiyan'
    IMAGE_NAME = 'workfront'
    TAG = 'latest'
  }

  stages {
    stage('Clone Repo') {
      steps {
        git clone 'https://github.com/Akash-Pandiyan/CICD.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:$TAG .'
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'echo $PASS | docker login -u $USER --password-stdin'
          sh 'docker push $DOCKERHUB_USER/$IMAGE_NAME:$TAG'
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl apply -f k8s/deployment.yaml'
        sh 'kubectl apply -f k8s/service.yaml'
        //sh 'kubectl rollout status deployment/flask-app'
      }
    }
  }

  post {
    success {
      echo '✅ Deployment successful!'
    }
    failure {
      echo '❌ Build or deployment failed!'
    }
  }
}
