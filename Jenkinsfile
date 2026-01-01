pipeline {
    agent any
    stages {
        stage('Check Tools') {
            steps {
                sh 'docker --version'
            }
        }
        stage('Build & Deploy') {
            steps {
                sh '''
                    docker rm -f my-app-container || true
                    docker build -t my-static-site .
                    docker run -d --name my-app-container -p 8081:80 my-static-site
                '''
            }
        }
    }
}
