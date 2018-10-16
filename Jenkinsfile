pipeline {
    agent { docker { image 'python:3.5.1' 
                     args '-u root:sudo' 
                     } }
    stages {
        stage('build') {
            steps {
                sh "python --version"
                sh "pip install -r requirements-dev.txt"
                sh "pip install pytest pytest-cov"
                sh "pip install coveralls"
                sh "pip install -e ."
                sh "py.test --doctest-modules --cov"

            }
        }
    }
}