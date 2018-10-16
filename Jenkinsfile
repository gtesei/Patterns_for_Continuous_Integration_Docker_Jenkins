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
        stage('build') {
            steps {
                sh "py.test --doctest-modules --cov"
            }
        }
        stage('test') {
            steps {
                sh "py.test --doctest-modules --cov"
            }
        }
    }
}