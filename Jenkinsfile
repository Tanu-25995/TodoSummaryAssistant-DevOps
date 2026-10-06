pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Backend Test') {
            steps {
                dir('Backend/todo-summary-assistant') {
                    sh './mvnw test'
                }
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t todo-backend:${BUILD_NUMBER} ./Backend/todo-summary-assistant'
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh 'docker build -t todo-frontend:${BUILD_NUMBER} ./Frontend/todo'
            }
        }
    }
}
