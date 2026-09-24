pipeline {
    agent any

    environment {
        NEXUS_URL = "nexus:8082"
        IMAGE_NAME = "puripatdach/helloapp"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/PuriDC/jenkins-example']])
            }
        }

        stage('SonarQube Analysis') {
            def scannerHome = tool 'sonarqube-scanner-8.1';
            withSonarQubeEnv() {
                sh "${scannerHome}/bin/sonar-scanner"
            }
        }
        
        stage('Build docker image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Login to Docker Hub'){
            steps{
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-credentials',
                        usernameVariable: 'DOCKERHUB_USERNAME',
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    )
                ]) {
                    sh "echo ${DOCKERHUB_PASSWORD} | docker login --username '${DOCKERHUB_USERNAME}' --password-stdin"
                }
            }
        } 

        stage('Push Image'){
            steps {
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }  
    }

    post {
        always {
            sh "docker logout || true"
        }
    }
}