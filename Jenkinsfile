pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'pawani2k2'
        DOCKERHUB_CREDENTIALS = credentials('docker-mine')
        GITHUB_CREDENTIALS = credentials('github-source')
        GITHUB_REPO = 'https://github.com/pabbico/output-public.git'
        MANIFEST_FILE = 'a.yaml'
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
                withCredentials([usernamePassword(credentialsId: 'docker-mine', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                    sh 'sudo docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD'
                    sh 'sudo docker push ${DOCKER_REGISTRY}/meri-sexy-repo:${IMAGE_TAG}'
                }
            }
        }

        stage('Update manifest file') {
            steps {
                sh """
                    sed -i 's/image: meri-sexy-repo:.*/image: meri-sexy-repo:${IMAGE_TAG}/' ${MANIFEST_FILE}
                    git add ${MANIFEST_FILE}
                    git commit -m 'Update manifest file with new image tag'
                    git remote set-url origin https://github.com/pabbico/output-public.git
                   
                    git push -u origin main
                """
            }
        }
    }
}
