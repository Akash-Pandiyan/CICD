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
        sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:$TAG .'
      }
    }
    
    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh '''
            echo $PASS | docker login -u $USER --password-stdin
            docker push $DOCKERHUB_USER/$IMAGE_NAME:$TAG
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          export KUBECONFIG=/var/lib/jenkins/.kube/config
          kubectl apply -f k8s/deployment.yaml --validate=false
          kubectl apply -f k8s/service.yaml --validate=false
          kubectl rollout status deployment/workfront
        '''
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