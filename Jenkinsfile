pipeline {
    agent any
    environment {
        IMAGE_NAME = "dhruti2005/jenkins-cicd"
        TAG = "${BUILD_NUMBER}"
    }
    stages {
        stage ('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/dhrutipandya123/jenkins_CICD.git'
            }
        }
        stage ('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
                sh 'docker tag $IMAGE_NAME:$TAG $IMAGE_NAME:latest'
            }
        }
        stage ('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
                sh 'docker push $IMAGE_NAME:$TAG'
                sh 'docker push $IMAGE_NAME:latest'
                sh 'docker logout'
            }
        }
        stage ('Deploy') {
            steps {
                sh 'docker stop student-app || true'
                sh 'docker rm student-app || true'
                sh 'docker pull $IMAGE_NAME:latest'
                sh 'docker run -d --name student-app -p 80:80 --restart unless-stopped $IMAGE_NAME:latest'
            }
        }
    }
}