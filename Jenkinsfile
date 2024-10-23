pipeline {
    agent any

    environment {
        VERSION = "${env.BUILD_ID}" // Or another versioning system
    }

    stages {
        stage('Build Nginx Server') {
            steps {
                script {
                    // Build the Docker image and tag it with both VERSION and 'latest'
                    def customImage = docker.build("pataljunior/nginx-docker:${VERSION}")
                    // Tag the image with 'latest'
                    sh "docker tag pataljunior/nginx-docker:${VERSION} pataljunior/nginx-docker:latest"
                }
            }
        }
        
        stage('Push') {
            steps {
                script {
                    // Authenticate and push both the versioned and 'latest' tags
                    withDockerRegistry(credentialsId: 'dockerhub-pataljunior') {
                        def customImage = docker.image("pataljunior/nginx-docker:${VERSION}")
                        customImage.push() // Push the versioned tag
                        customImage.push('latest') // Push the 'latest' tag
                    }
                }
            }
        }

        stage('Watchtower Update') {
            steps {
                script {
                    sh 'curl -H "Authorization: Bearer patalToken" 192.168.1.115:8080/v1/update'
                }
            }
        }
    }
}
