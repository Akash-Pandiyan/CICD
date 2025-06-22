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
            sh docker login -u $USER-p $PASS
            sh docker push $DOCKERHUB_USER/$IMAGE_NAME:$TAG

          //withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          //bat 'echo %PASS | docker login -u $USER --password-stdin'
          //bat 'docker push %DOCKERHUB_USER%/%IMAGE_NAME%:%TAG'
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        bat '''
            set KUBECONFIG=C:\\ProgramData\\Jenkins\\.kube\\config
            kubectl apply -f k8s\\deployment.yaml --validate=false
            kubectl apply -f k8s\\service.yaml --validate=false
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
