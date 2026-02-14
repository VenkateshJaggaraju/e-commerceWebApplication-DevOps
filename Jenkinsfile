pipeline {
    agent any

    environment {
        IMAGE_NAME = "venkateshjaggaraju/static-webapp"
        IMAGE_TAG = "v1"
        DOCKERHUB_CREDENTIALS = "dockerhub-creds"
    }

    stages {

        stage('Stage-1: Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
            post {
                success {
                    echo "Docker image built successfully"
                }
                failure {
                    echo "Docker image build failed"
                }
            }
        }

        stage('Stage-2: Tag Docker Image') {
            steps {
                sh "docker tag ${IMAGE_NAME}:latest ${IMAGE_NAME}:${IMAGE_TAG}"
            }
            post {
                success {
                    echo "Docker image tagged successfully"
                }
                failure {
                    echo "Docker tag failed"
                }
            }
        }

        stage('Stage-3: Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                }
            }
            post {
                success {
                    echo "DockerHub login successful"
                }
                failure {
                    echo "DockerHub login failed"
                }
            }
        }

        stage('Stage-4: Push Image to DockerHub') {
            steps {
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
            post {
                success {
                    echo "Image pushed to DockerHub successfully"
                }
                failure {
                    echo "Docker push failed"
                }
            }
        }

        stage('Stage-5: Remove Image Locally') {
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true"
            }
            post {
                success {
                    echo "Local Docker image removed"
                }
                failure {
                    echo "Failed to remove local image"
                }
            }
        }

        stage('Stage-6: Trigger Ansible Playbook') {
            steps {
                sh "ansible-playbook deploy.yml"
            }
            post {
                success {
                    echo "Ansible playbook executed successfully"
                }
                failure {
                    echo "Ansible playbook execution failed"
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline execution finished"
        }
        success {
            echo "Pipeline completed successfully 🎉"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}
