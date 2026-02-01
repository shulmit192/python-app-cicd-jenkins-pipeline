pipeline {
    agent {
        docker {
            image 'python:3.11'  // runs all stages inside a Python Docker container
        }
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }

        stage('Setup Python Environment') {
            steps {
                echo 'Setting up Python virtual environment...'
                sh '''
                    python -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install pytest pytest-html pytest-cov
                '''
            }
        }

        stage('Build / Compile Check') {
            steps {
                echo 'Checking Python files for syntax errors...'
                sh '''
                    . venv/bin/activate
                    python -m py_compile app.py
                '''
            }
        }

        stage('Unit Test') {
            steps {
                echo 'Running unit tests with pytest...'
                sh '''
                    . venv/bin/activate
                    mkdir -p test-reports
                    pytest test.py --junitxml=test-reports/results.xml --html=test-reports/report.html --self-contained-html
                '''
            }
            post {
                always {
                    echo 'Publishing test reports...'
                    junit 'test-reports/results.xml'
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'test-reports',
                        reportFiles: 'report.html',
                        reportName: 'Pytest HTML Report'
                    ])
                }
            }
        }
    }

    post {
        success {
            echo 'Python CI Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

