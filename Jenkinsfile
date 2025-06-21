pipeline {
  agent any

  environment {
    DOCKERHUB_USER = 'akashpandiyan'
    IMAGE_NAME = 'workfront'
    TAG = 'v1'
  }

  stages {
    stage('Clone Repo') {
      steps {
        git 'https://github.com/Akash-Pandiyan/CICD.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        bat 'docker build -t %DOCKERHUB_USER%/%IMAGE_NAME%:%TAG .'
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          bat 'echo %PASS | docker login -u $USER --password-stdin'
          bat 'docker push %DOCKERHUB_USER%/%IMAGE_NAME%:%TAG'
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        bat 'kubectl apply -f k8s/deployment.yaml'
        bat 'kubectl apply -f k8s/service.yaml'
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
