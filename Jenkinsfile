pipeline {
    agent { docker { image 'python:3.5.1' } }
    stages {
        stage('build') {
            steps {
                sh 'python --version'
                sh 'apt-get install python-pip python-dev build-essential'
                sh 'pip install --upgrade pip'
                sh 'pip install --upgrade virtualenv'
                sh 'pip install -r requirements-dev.txt'
                sh 'pip install pytest pytest-cov' 
                sh 'pip install coveralls'
                sh 'pip install -e .'
                sh 'py.test --doctest-modules --cov'

            }
        }
    }
}