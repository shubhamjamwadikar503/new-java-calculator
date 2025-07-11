pipeline {
    agent any

    environment {
        DOCKER_USERNAME = 'shubhamjamwadikar503' // Replace with your DockerHub username
        DOCKER_IMAGE = "${DOCKER_USERNAME}/crud-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Pulling code from GitHub...'
                git branch: 'main', url: 'https://github.com/shubhamjamwadikar503/new-java-calculator.git'
            }
        }

        stage('Maven Build') {
            steps {
                echo 'Building application with Maven...'
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh '''
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo 'Pushing Docker image to DockerHub...'
                withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest
                        docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }
/*
        stage('Deploy Container') {
            steps {
                echo 'Deploying Docker container...'
                sh '''
                    docker stop calculator || true
                    docker rm calculator || true
                    docker system prune -a -f
                    docker run -d -p 8082:8080 --name calculator ${DOCKER_IMAGE}:${DOCKER_TAG}
                '''
            }
        }
*/
        stage('Deploy to Minikube') {
            steps {
                echo 'Deploying to Minikube...'
                sh '''
                    kubectl delete deployment calculator --ignore-not-found
                    kubectl delete service calculator-service --ignore-not-found
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    kubectl get pods
                '''
    }
}

    }

}
