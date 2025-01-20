pipeline {
    agent any

    tools {
        maven 'maven'  
    }

    environment {
        DOCKER_IMAGE = 'sathvik1522/reactimage'  
        DOCKER_TAG = 'latest'
        CREDENTIAL = 'docker_credential'         
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/sathvik5088/SPRINGBOOT.git'
            }
        }

        stage('Build jar') {
            steps {
                sh 'mvn clean package'  
            }
        }
        
        stage('CleanUp Docker Images and Containers') {
            steps {
                script {
                    // Stop and remove all containers (running or stopped), but only if any exist
                    sh '''
                        CONTAINERS=$(docker ps -aq)
                        if [ -n "$CONTAINERS" ]; then
                            docker rm -f $CONTAINERS
                        else
                            echo "No containers to remove."
                        fi
                    '''
                    
                    // Remove all dangling images, but only if any exist
                    sh '''
                        IMAGES=$(docker images -q -f dangling=true)
                        if [ -n "$IMAGES" ]; then
                            docker rmi -f $IMAGES
                        else
                            echo "No dangling images to remove."
                        fi
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."  // Build the Docker image
            }
        }

        // stage('Push Docker Image') {
        //     steps {
        //         script {
        //             // Log in to DockerHub and push the Docker image
        //             docker.withRegistry('https://registry.hub.docker.com', CREDENTIAL) {
        //                 sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
        //             }
        //         }
        //     }
        // }
        
        
        stage('Run the Container') {
            steps {
                sh "docker run -it -d --name my-container -p 8081:8081 ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }
}
