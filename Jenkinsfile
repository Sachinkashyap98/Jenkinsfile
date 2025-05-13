pipeline {
    agent any

    environment {
        ACR_LOGIN_SERVER = 'jenkin.azurecr.io'  // Replace with your ACR login server
        IMAGE_NAME = 'nodejs'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Docker Login to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'acr-creds', usernameVariable: 'ACR_USER', passwordVariable: 'ACR_PASS')]) {
                    sh '''
                        echo $ACR_PASS | docker login $ACR_LOGIN_SERVER -u $ACR_USER --password-stdin
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Push Docker Image to ACR') {
            steps {
                sh '''
                    docker push $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
