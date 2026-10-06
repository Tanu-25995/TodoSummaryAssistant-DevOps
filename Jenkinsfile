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
               sh '''
                SPRING_DATASOURCE_URL="jdbc:mysql://todo-mysql:3306/todo_db?createDatabaseIfNotExist=true" \
                SPRING_DATASOURCE_USERNAME="root" \
                SPRING_DATASOURCE_PASSWORD="todo_root_password" \
                ./mvnw test
            '''
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
