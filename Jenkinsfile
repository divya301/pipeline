pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your-docker-image-name"
        DOCKER_REGISTRY = "your-docker-registry"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_ID}")
                }
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm ${DOCKER_IMAGE}:${BUILD_ID} ./run-tests.sh'
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry("${DOCKER_REGISTRY}", 'docker-credentials-id') {
                        dockerImage.push("${BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
