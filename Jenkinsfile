pipeline {
    agent { docker { image 'python:3.5.1' 
                     args '-u root:sudo' 
                     } }
    stages {
        stage('build') {
            steps {
                sh "python --version"
                sh "apt-get install python-pip python-dev build-essential"
                sh "sudo pip install --upgrade pip"
                sh "sudo pip install --upgrade virtualenv"
                sh "sudo pip install -r requirements-dev.txt"
                sh "sudo pip install pytest pytest-cov"
                sh "sudo pip install coveralls"
                sh "sudo pip install -e ."
                sh "py.test --doctest-modules --cov"

            }
        }
    }
}