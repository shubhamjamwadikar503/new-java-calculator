pipeline {
    agent any

    environment {
        DOCKER_USERNAME = 'shubhamjamwadikar503'           // your DockerHub username
        DOCKER_IMAGE = "${DOCKER_USERNAME}/crud-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'     // Jenkins credentials ID
        KUBECONFIG = '/var/lib/jenkins/.kube/config'        // Path to kubeconfig
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Pulling code from GitHub...'
                git branch: 'main', url: 'https://github.com/shubhamjamwadikar503/new-java-calculator.git'
            }
        }

        stage('Build with Maven') {
            steps {
                echo 'Building project with Maven...'
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                echo 'Pushing Docker image to DockerHub...'
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest
                        docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                echo 'Deploying to Minikube...'
                withEnv(['KUBECONFIG=/var/lib/jenkins/.kube/config']) {

                sh '''
                    kubectl delete deployment calculator-deployment --ignore-not-found
                    kubectl delete service calculator-service --ignore-not-found
                    kubectl apply -f ./deployment.yaml
                    kubectl apply -f ./service.yaml
                '''
            }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get svc'
            }
        }
    }
}
