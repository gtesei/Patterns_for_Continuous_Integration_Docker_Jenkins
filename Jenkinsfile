pipeline {
    agent { docker { image 'python:3.5.1' 
                     args '-u root:sudo' 
                     } }
    stages {
        stage('install') {
            steps {
                sh "python --version"
                sh "pip install -r requirements-dev.txt"
                sh "pip install pytest pytest-cov"
                sh "pip install coveralls"
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
            sh "COVERALLS_REPO_TOKEN=$COVERALLS_REPO_TOKEN COVERALLS_HOST="https://coveralls.io" coveralls"
          }
        }
    }
}