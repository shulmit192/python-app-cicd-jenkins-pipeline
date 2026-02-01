pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '''
                    python3 --version
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Build / Compile Check') {
            steps {
                sh '''
                    . venv/bin/activate
                    python -m py_compile app.py
                '''
            }
        }

        stage('Unit Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest --html=report.html --self-contained-html
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'report.html', fingerprint: true
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
    }
}
