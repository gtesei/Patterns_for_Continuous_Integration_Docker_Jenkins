pipeline {
    agent any

    stages {
        
        stage('docker') {
          
          environment {
            IMAGE_NAME = 'gtesei/hello_docker_jenkins'
            DOCKER_REGISTRY = credentials('3897e886-55e9-491f-95f9-bf0280b72966')
          }
          steps {
            sh "docker pull $IMAGE_NAME || true"
            sh 'docker build --pull --cache-from "${IMAGE_NAME}:develop" --tag "$IMAGE_NAME" . || true'
            sh 'docker login -u $DOCKER_REGISTRY_USR -p $DOCKER_REGISTRY_PSW'
            sh 'docker tag "$IMAGE_NAME" "${IMAGE_NAME}:${git_sha}-develop"'
            sh 'docker push "${IMAGE_NAME}:develop"'
          }
        }
    }
}