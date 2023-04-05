pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'pawani2k2'
        DOCKERHUB_CREDENTIALS = credentials('docker-public')
        GITHUB_CREDENTIALS = credentials('output-public')
        GITHUB_REPO = 'https://github.com/pabbico/output-public.git'
        MANIFEST_FILE = 'nginx-deployment.yaml'
        BUILD_ID = sh(script: "echo ${BUILD_ID}", returnStdout: true).trim()
        IMAGE_TAG = "v${BUILD_ID}"
    }

    stages {

        stage('Build Docker image') {
            steps {
                // Build the Docker image with a unique tag
                sh "sudo docker build -t ${DOCKER_REGISTRY}/meri-sexy-repo:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker image') {
            steps {
                // Login to Docker Hub and push the Docker image with the unique tag
                withCredentials([usernamePassword(credentialsId: 'docker-public', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                    sh 'sudo docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD'
                    sh 'sudo docker push ${DOCKER_REGISTRY}/meri-sexy-repo:${IMAGE_TAG}'
                }
            }
        }
         stage('Checkout') {
            steps {
                // Checkout the source code from the GitHub repository
                git credentialsId: 'output-public', url: GITHUB_REPO
            }
        }


        stage('Update manifest file') {
            steps {
                // Update the Kubernetes manifest file with the new image tag
                withCredentials([usernamePassword(credentialsId: 'output-public', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_PASSWORD')]) {
                    sh """
                        sed -i "s/image: pawani2k2\\/meri-sexy-repo.*/image: pawani2k2\\/meri-sexy-repo:${IMAGE_TAG}/" ${MANIFEST_FILE}
                        git config --global user.name "pawan"
                        git config --global user.email "pawan.sharma@i2k2.com"
                        git add ${MANIFEST_FILE}
                        git commit -m 'Update manifest file with new image tag'
                        git remote set-url origin https://${GITHUB_USERNAME}:${GITHUB_PASSWORD}@github.com/pabbico/output-public.git
                        git push origin main
                    """
                }
            }
        }
    }
}
