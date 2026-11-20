pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'gnanaprasanna1206'
        IMAGE_NAME     = "${DOCKERHUB_USER}/spring-boot-app"
        IMAGE_TAG      = "${BUILD_NUMBER}"
    }

    stages {
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh 'echo $DH_PASS | docker login -u $DH_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                }
            }
        }
    }

        post {
        success {
            emailext to: 'prasanna.g1206@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build passed. Image ${IMAGE_NAME}:${IMAGE_TAG} pushed. Details: ${env.BUILD_URL}"
        }
        failure {
            emailext to: 'prasanna.g1206@gmail.com',
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build failed. Check the console output: ${env.BUILD_URL}console"
        }
        always {
            sh 'docker logout || true'
        }
    }
}
