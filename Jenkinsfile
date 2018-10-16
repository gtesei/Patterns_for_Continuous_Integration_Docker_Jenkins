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
          steps {
            sh "coverage run --source=python_dev_docker_project setup.py test"
            sh "COVERALLS_REPO_TOKEN=credentials('coveralls_token') coveralls"
          }
        }
    }
}