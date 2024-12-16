pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "rahuls001/demo-app"
        PATH = "${tool 'docker'}/bin:${env.PATH}"
        EMAIL_RECIPIENT = "rkrahulsh001@gmail.com"
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                    docker build -t ${DOCKER_IMAGE}:latest .
                    """
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        sh """
                        docker login -u rahuls001 -p $DOCKER_PASS
                        docker push ${DOCKER_IMAGE}:latest
                        """
                    }
                }
            }
        }
        stage('Deploy Application') {
            steps {
                script {
                    sh """
                    docker service rm demo-app || true
                    docker service create --name demo-app --replicas 2 -p 3000:3000 ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
    }
    post {
        success {
            mail to: "${EMAIL_RECIPIENT}",
                 subject: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "The Jenkins job ${env.JOB_NAME} completed successfully. Build #${env.BUILD_NUMBER}"
        }
        failure {
            mail to: "${EMAIL_RECIPIENT}",
                 subject: "Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "The Jenkins job ${env.JOB_NAME} failed. Build #${env.BUILD_NUMBER}"
        }
    }
}
