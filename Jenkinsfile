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
            withCredentials([usernamePassword(
                credentialsId: 'database-credentials',
                usernameVariable: 'DB_USERNAME',
                passwordVariable: 'DB_PASSWORD'
            )]) {
                sh '''
                   docker rm -f todo-mysql-test 2>/dev/null || true
docker run -d --name todo-mysql-test \
  --network todosummaryassistant_todo-network \
  --network-alias todo-mysql \
  -e MYSQL_DATABASE=todo_db \
  -e MYSQL_ROOT_PASSWORD="$DB_PASSWORD" \
  mysql:8.0

for i in $(seq 1 30); do
  if docker exec todo-mysql-test mysqladmin ping -h localhost -uroot -p"$DB_PASSWORD" --silent; then
    break
  fi
  sleep 2
done
                    SPRING_DATASOURCE_URL="jdbc:mysql://todo-mysql:3306/todo_db?createDatabaseIfNotExist=true" \
                    SPRING_DATASOURCE_USERNAME="$DB_USERNAME" \
                    SPRING_DATASOURCE_PASSWORD="$DB_PASSWORD" \
                    ./mvnw test
                '''
            }
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
                sh 'docker build --build-arg REACT_APP_API_URL=http://13.127.186.93:8080/api/todos -t tanud/todo-frontend:${GIT_COMMIT} ./Frontend/todo'
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
	        stage('Deploy to EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ec2-ssh-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    sh '''
                        ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no "$SSH_USER@13.127.186.93" "
                            set -e
                            sudo docker pull tanud/todo-backend:${GIT_COMMIT}
                            sudo docker pull tanud/todo-frontend:${GIT_COMMIT}
                            sudo docker tag tanud/todo-backend:${GIT_COMMIT} todo-backend:ec2
                            sudo docker tag tanud/todo-frontend:${GIT_COMMIT} todo-frontend:ec2
                            cd ~/TodoSummaryAssistant-DevOps
                            sudo docker compose up -d backend frontend
                        "
                    '''
                }
            }
        }

        stage('Post-deployment Health Check') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ec2-ssh-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    sh '''
                        ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no "$SSH_USER@13.127.186.93" '
                            for i in $(seq 1 12); do
                                if curl -fsS http://localhost:8080/actuator/health >/dev/null &&
                                   curl -fsS http://localhost:3000/ >/dev/null; then
                                    echo "Deployment health check passed"
                                    exit 0
                                fi
                                sleep 5
                            done
                            echo "Deployment health check failed"
                            exit 1
                        '
                    '''
                }
            }
        }
    }
}
