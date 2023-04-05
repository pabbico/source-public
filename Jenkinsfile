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
                sh "sudo docker build -t ${DOCKER_REGISTRY}/meri-sexy-repo:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-public', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                    sh 'sudo docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD'
                    sh 'sudo docker push ${DOCKER_REGISTRY}/meri-sexy-repo:${IMAGE_TAG}'
                }
            }
        }

        stage('Update manifest file') {
            steps {
                git credentialsId: 'output-public', url: GITHUB_REPO
                sh """
                    sed -i 's|image: pawani2k2/meri-sexy-repo.*|image: ${DOCKER_REGISTRY}/meri-sexy-repo:${IMAGE_TAG}|' ${MANIFEST_FILE}
                    git config user.email "pawan.sharma@i2k2.com"
                    git config user.name "pawan"
                    git commit -a -m 'Update manifest file with new image tag'
                    git push
                """
            }
        }
    }
}
