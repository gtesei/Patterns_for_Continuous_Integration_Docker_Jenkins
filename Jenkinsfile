pipeline {
    agent { docker { image 'python:3.6.1' 
                     args '-u root:sudo' 
                     } }
    stages {
        stage('install') {
            steps {
                sh "python --version"
                sh "pip install --upgrade pip"
                sh "pip install -r requirements-dev.txt"
                sh "pip install pytest pytest-cov"
                sh "pip install coveralls"
                sh "pip install coverage"
            }
        }
        stage('build') {
            steps {
                sh "pip install -e ."
            }
        }
        stage('test') {
            steps {
                sh "py.test --doctest-modules --cov"
            }
        }
        stage('coveralls') {
          environment {
            COVERALLS_REPO_TOKEN = credentials('d79070bd-2e2b-4a91-ba27-e1ecd68897ad')
          }
          steps {
            sh "echo $COVERALLS_REPO_TOKEN"
            sh "coverage run setup.py test"
            sh "py.test --doctest-modules --cov"
            sh "coverage report -m"
            sh "COVERALLS_REPO_TOKEN=$COVERALLS_REPO_TOKEN_PSW coveralls"
          }
        }
        stage('docker') {
          environment {
            IMAGE_NAME = 'gtesei/hello_docker_jenkins'
            DOCKER_REGISTRY = credentials('3897e886-55e9-491f-95f9-bf0280b72966')
          }
          steps {
            sh "docker pull $IMAGE_NAME || true"
            sh 'docker build --pull --cache-from "${IMAGE_NAME}:develop" --tag "$IMAGE_NAME" .'
            sh 'docker login -u $DOCKER_REGISTRY_USR -p "$DOCKER_REGISTRY_PWD'
            sh 'git_sha="$(git rev-parse --short HEAD)"'
            sh 'docker tag $IMAGE_NAME "${IMAGE_NAME}:develop"'
            sh 'docker tag "$IMAGE_NAME" "${IMAGE_NAME}:${git_sha}-develop"'
            sh 'docker push "${IMAGE_NAME}:develop" && docker push "${IMAGE_NAME}:${git_sha}-develop"'
          }
        }
    }
}