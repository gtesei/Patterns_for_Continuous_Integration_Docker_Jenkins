pipeline {
    agent { docker { image 'python:3.5.1' } }
    environment {
        PATH = "/usr/local/bin:/usr/bin:/bin"
    }
    stages {
        stage('build') {
            steps {
                sh 'python --version'
                sh 'sudo pip install -r requirements-dev.txt'
                sh 'sudo pip install pytest pytest-cov' 
                sh 'sudo pip install coveralls'
                sh 'sudo pip install -e .'
                sh 'py.test --doctest-modules --cov'

            }
        }
    }
}