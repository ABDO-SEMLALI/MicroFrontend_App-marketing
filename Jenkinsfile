pipeline {
  agent any

  parameters {
    string(name: 'IMAGE_NAME', defaultValue: 'marketing', description: 'Docker image name')
    string(name: 'DOCKERHUB_USERNAME', defaultValue: 'ash0semlali', description: 'Docker Hub username')
  }

  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }

  stages {
    stage('Checkout code') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        bat "docker build -t ${params.IMAGE_NAME}:latest ."
      }
    }

    stage('Test') {
      steps {
        echo "test passed"
      }
    }

    stage('Push Images to Docker Hub') {
      steps {
        bat "echo %DOCKERHUB_CREDENTIALS_PSW%| docker login -u ${params.DOCKERHUB_USERNAME} --password-stdin"
        bat "docker tag ${params.IMAGE_NAME}:latest ${params.DOCKERHUB_USERNAME}/${params.IMAGE_NAME}:latest"
        bat "docker push ${params.DOCKERHUB_USERNAME}/${params.IMAGE_NAME}:latest"
      }
    }

    stage('Cleanup') {
        steps {
          sh 'docker logout'
        }
      }
  }
  post {
    success {
      mail bcc: '', body: """
      Le pipeline Jenkins s'est exécuté avec succès le ${currentBuild.startTimeInMillis}.
      Tout s'est déroulé sans erreur.
      Voici le lien de l'application si vous souhaitez le consulter
      """, subject: 'Sujet : Reussite du pipeline Jenkins', to: env.EMAIL_LIST
    }
    failure {
      mail bcc: '', body: """
      Le pipeline Jenkins a échoué le ${currentBuild.startTimeInMillis}.
      Veuillez prendre les mesures nécessaires pour résoudre le problème.
      """, subject: 'Sujet : Echec du pipeline Jenkins', to: env.EMAIL_LIST
    }
  }
}
