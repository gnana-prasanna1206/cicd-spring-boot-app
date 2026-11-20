pipeline {
agent any

environment {
IMAGE_NAME = "gnanaprasanna1206/spring-boot-app"
IMAGE_TAG = "${BUILD_NUMBER}"
}

stages {
stage('Checkout') {
steps {
checkout scm
}
}

stage('Maven Build') {
steps {
sh 'mvn clean package'
}
}

stage('Docker Build') {
steps {
sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
}
}

stage('Trivy Scan') {
steps {
sh 'trivy image --severity HIGH,CRITICAL --exit-code 0 $IMAGE_NAME:$IMAGE_TAG'
}
}

stage('Push to Docker Hub') {
steps {
withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
}
}
}
}

post {
always {
sh 'docker logout'
}
}
}
