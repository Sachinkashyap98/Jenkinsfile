pipeline {
    agent any

    environment {
        ACR_LOGIN_SERVER = 'jenkin.azurecr.io'     // change to your ACR login server
        IMAGE_NAME = 'nodejs'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Check Branch') {
            steps {
                script {
                    if (env.BRANCH_NAME != 'jenkin') {
                        currentBuild.result = 'ABORTED'
                        error "This pipeline only runs on the 'jenkin' branch"
                    }
                }
            }
        }

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
                    docker images  # Verify the image exists locally
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

        // Uncomment and fill in if you want to deploy to Azure Container Instance (ACI)
        // stage('Deploy to Azure Container Instance') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'acr-creds', usernameVariable: 'ACR_USER', passwordVariable: 'ACR_PASS')]) {
        //             sh '''
        //                 az login --service-principal -u $ACR_USER -p $ACR_PASS --tenant <your-tenant-id>
        //                 az container create \
        //                   --resource-group <your-resource-group> \
        //                   --name nginx-demo \
        //                   --image $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG \
        //                   --registry-login-server $ACR_LOGIN_SERVER \
        //                   --registry-username $ACR_USER \
        //                   --registry-password $ACR_PASS \
        //                   --dns-name-label nginx-demo-$BUILD_NUMBER \
        //                   --ports 80
        //             '''
        //         }
        //     }
        // }
    }
}
