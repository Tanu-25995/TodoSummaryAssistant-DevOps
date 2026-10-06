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
                sh 'docker build -t tanud/todo-backend:${GIT_COMMIT} ./Backend/todo-summary-assistant'
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh 'docker build -t tanud/todo-frontend:${GIT_COMMIT} ./Frontend/todo'
            }
        }
        
        stage('Push Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push tanud/todo-backend:${GIT_COMMIT}
                        docker push tanud/todo-frontend:${GIT_COMMIT}
                    '''
                }
            }
        }
    }
}
