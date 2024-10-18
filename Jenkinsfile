pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'asi_insurance'  // Replace with your Docker image name
    }

    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Build") {
            steps {
                // Install npm if needed and build the application
                sh 'sudo apt update'
                sh 'sudo apt install -y npm'
                sh 'npm install'  // Ensure dependencies are installed
                sh 'npm run build'
            }
        }

        stage("Build Image") {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:1.0 ."
            }
        }

        stage("Docker Push") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    // Log in to Docker Hub
                    sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                    
                    // Tag the image
                    sh "docker tag ${DOCKER_IMAGE}:1.0 prashanthoct74/${DOCKER_IMAGE}:1.0"
                    
                    // Push the image to Docker Hub
                    sh "docker push prashanthoct74/${DOCKER_IMAGE}:1.0"
                    
                    // Log out from Docker Hub
                    sh 'docker logout'
                }
            }
        }

        stage("Deploy Docker Image") {
            steps {
                script {
                    // Pull the latest image (optional, can be skipped if the image is built in this pipeline)
                    sh "docker pull prashanthoct74/${DOCKER_IMAGE}:1.0"

                    // Stop and remove any existing container
                    sh """
                    docker stop my_container || true
                    docker rm my_container || true
                    
                    // Run the new container
                    docker run -d --name my_container -p 80:80 prashanthoct74/${DOCKER_IMAGE}:1.0  # Adjust ports as needed
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
