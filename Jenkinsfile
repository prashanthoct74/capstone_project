pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'asi_insurance'  // Your Docker image name
        IMAGE_VERSION = '1.0'            // Version variable for easy updates
    }

    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Build Image") {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${IMAGE_VERSION} ."
            }
        }

        stage("Push Image") {
            steps {
                withDockerRegistry([ credentialsId: "docker_cred", url: "https://index.docker.io/v1/" ]) {
                    // Tag the image
                    sh "docker tag ${DOCKER_IMAGE}:${IMAGE_VERSION} prashanthoct74/${DOCKER_IMAGE}:${IMAGE_VERSION}"
                    
                    // Push the image to Docker Hub
                    sh "docker push prashanthoct74/${DOCKER_IMAGE}:${IMAGE_VERSION}"
                }
            }
        }


        stage("Deploy Docker Image") {
            steps {
                script {
                    // Pull the latest image (optional)
                    sh "docker pull prashanthoct74/${DOCKER_IMAGE}:${IMAGE_VERSION}"

                    // Stop and remove any existing container
                    sh """
                    docker stop my_container || true
                    docker rm my_container || true
                    
                    // Run the new container
                    docker run -d --name my_container -p 80:80 prashanthoct74/${DOCKER_IMAGE}:${IMAGE_VERSION}  // Adjust ports as needed
                    """
                }
            }
        }
    }

    post {
        always {
            // Notify on completion
            echo 'Pipeline completed.'
        }
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
