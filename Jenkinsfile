/* groovylint-disable CompileStatic, DuplicateStringLiteral, NestedBlockDepth */
pipeline {
    agent any

    stages {

        stage('Build and Push Docker Image') {
            environment {
                DOCKER_REGISTRY = 'neroxxpips'
                DOCKER_IMAGE_NAME = 'jenkins-demo'
                DOCKER_IMAGE_TAG = 'latest'
                DOCKER_PASSWORD = credentials('docker-token')
                DOCKER_USERNAME = 'neroxxpips'
            }
            steps {
                sh 'docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG .'
                sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
                sh 'docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
                sh 'docker logout'
            }
        }

        stage('Integrate Jenkins with EKS Cluster and Deploy App') {
            environment {
                EKS_CLUSTER_NAME = 'jenkins-demo'
                AWS_REGION = 'us-east-1'
            }
            steps {
                withAWS(credentials: 'aws-credentials', region: 'us-east-1') {
                    script {
                        sh """
                        aws eks update-kubeconfig --name $EKS_CLUSTER_NAME --region $AWS_REGION
                        kubectl get node
                        kubectl create deployment jenkins-demo --image=neroxxpips/jenkins-demo:latest
                        """
                    }
                }
            }
        }
    }
}
