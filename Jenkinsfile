pipeline {

    agent any

    environment {
        registry = "gamalm2041/myapp_image"
        registryCredential = 'dockerhub'
    }

    stages {
        stage('Build Artifact WAR File') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t app_from_jenkins:v$BUILD_NUMBER .'
            }
            post {
                success {
                    echo 'Docker Image Successfully Built'
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                sh 'docker run -d --name lab$BUILD_NUMBER -p 7070:8080 app_from_jenkins:v$BUILD_NUMBER'
            }
            post {
                success {
                    echo 'Docker Container Successfully Tested'
                }
            }
        }

        stage('Remove Container') {
            steps {
                sh 'docker rm -f lab$BUILD_NUMBER'
            }
            post {
                success {
                    echo 'Docker Container Successfully Removed'
                }
            }
        }

        stage('Deploy App on Kubernetes') {
            steps {
                // Apply Kubernetes manifests using regular kubectl
                sh 'kubectl apply -f kubernetes/'
            }
            post {
                success {
                    echo 'App Successfully Deployed to Kubernetes'
                }
            }
        }
    }
}
