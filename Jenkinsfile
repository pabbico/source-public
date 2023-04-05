pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'pawani2k2'
        DOCKERHUB_CREDENTIALS = credentials('docker-public')
        GITHUB_CREDENTIALS = credentials('output-public')
        GITHUB_REPO = 'https://github.com/pabbico/output2-public.git'
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
                withCredentials([usernamePassword(credentialsId: 'output-public', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_PASSWORD')]) {
                    sh """
                        pwd
                        sed -i 's/image: pawani2k2\/meri-sexy-repo.*/image: pawani2k2\/meri-sexy-repo:${BUILD_ID}/' ${MANIFEST_FILE} // Escape the forward slashes in the sed command
                        git clone -b main ${GITHUB_REPO} // Clone the repository instead of setting the remote URL
                        cd output-public // Change directory to the cloned repository
                        git checkout main // Checkout the main branch
                        cp ${WORKSPACE}/${MANIFEST_FILE} ./ // Copy the updated manifest file to the cloned repository
                        git add ${MANIFEST_FILE}
                        git commit -m 'Update manifest file with new image tag'
                        git push --set-upstream origin main // Use --set-upstream to set the upstream branch
                    """
            }
        }
    }
}
}
