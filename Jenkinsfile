pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven-3.9.16'
    }

    environment {
        IMAGE_NAME = "praveensusen/student-management-system"
        IMAGE_TAG = "latest"
        KUBECONFIG = "C:\\Users\\susen\\.kube\\config"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

       stage('Build Maven') {
    steps {
        bat 'mvn clean package -DskipTests'
    }
}

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%IMAGE_TAG% .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat '''
                    docker logout
                    docker login -u %DOCKER_USER% --password %DOCKER_PASS%
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                    '''
                }
            }
        }

        stage('Check User') {
            steps {
                bat '''
                whoami
                echo %USERPROFILE%
                '''
            }
        }

        stage('Check Kubernetes') {
            steps {
                bat '''
                echo KUBECONFIG=%KUBECONFIG%
                kubectl config current-context
                kubectl cluster-info
                kubectl get nodes
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                kubectl apply -f k8s/student-deployment.yaml
                kubectl apply -f k8s/student-service.yaml
                '''
            }
        }
    }
}