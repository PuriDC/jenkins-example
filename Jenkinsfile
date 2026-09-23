pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/PuriDC/jenkins-example.git']])
            }
        }
    }
}