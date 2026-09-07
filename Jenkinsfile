pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'vishwajits7/jenkins-docker-demo'        
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh 'javac src/Main.java'
                sh 'java -cp src Main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry(
                        'https://registry.hub.docker.com',
                        "${REGISTRY_CREDENTIALS}"
                    ) {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker stop java-app || true'
                sh 'docker rm java-app || true'
                sh "docker run -d --name java-app ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
            }
        }
    }
}