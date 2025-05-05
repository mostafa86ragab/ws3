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

        stage('Build Podman Image') {
            steps {
                // Replace 'docker' with 'podman' to build the image
                sh 'podman build -t app_from_jenkins:v$BUILD_NUMBER .'
            }
            post {
                success {
                    echo 'Podman Image Successfully Built'
                }
            }
        }

        stage('Test Podman Image') {
            steps {
                // Replace 'docker' with 'podman' to run the container
                sh 'podman run -d --name lab$BUILD_NUMBER -p 7070:8080 app_from_jenkins:v$BUILD_NUMBER'
            }
            post {
                success {
                    echo 'Podman Container Successfully Tested'
                }
            }
        }

        stage('Remove Container') {
            steps {
                // Replace 'docker' with 'podman' to remove the container
                sh 'podman rm -f lab$BUILD_NUMBER'
            }
            post {
                success {
                    echo 'Podman Container Successfully Removed'
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
