pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building Node.js application...'
                dir('src') {
                    sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                dir('src') {
                    sh 'npm start &'
                    sh 'sleep 5'
                    sh 'npm test'
                }
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -f build/Dockerfile -t nodejs-demoapp:jenkins .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying container...'
                sh 'docker rm -f nodejs-jenkins-app || true'
                sh 'docker run -d -p 3005:3000 --name nodejs-jenkins-app nodejs-demoapp:jenkins'
            }
        }
    }
}