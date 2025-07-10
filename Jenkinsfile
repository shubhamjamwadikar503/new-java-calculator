pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = '/crud-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
//        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'
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
                    docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest
                '''
            }
        }
/*        
        stage('Push to DockerHub') {
            steps {
                echo 'Pushing Docker image to DockerHub...'
                withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh '''
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }
*/
    }
    
}
