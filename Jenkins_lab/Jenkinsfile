pipeline {
    agent any

    environment {
        VENV = "venv"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out source..."
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                echo "Creating virtualenv and installing dependencies..."
                sh '''
                    set -e
                    python3 -m venv ${VENV}
                    . ${VENV}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Build step (packaging artifact)..."
                sh '''
                    set -e
                    . ${VENV}/bin/activate
                    mkdir -p dist
                    # create a simple zip as build artifact (adjust as needed)
                    zip -r dist/${JOB_NAME}-${BUILD_NUMBER}.zip app.py requirements.txt -x "*.pyc" || true
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running tests with pytest..."
                sh '''
                    set -e
                    . ${VENV}/bin/activate
                    mkdir -p reports
                    pytest -q --junitxml=reports/junit.xml
                '''
            }
            post {
                always {
                    junit 'reports/junit.xml'
                }
            }
        }

        stage('Archive') {
            steps {
                echo "Archiving artifacts..."
                archiveArtifacts artifacts: 'dist/*.zip', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Pipeline Succeeded"
        }
        failure {
            echo "Pipeline Failed"
        }
        always {
            echo "Cleaning workspace (optional)..."
            // You can uncomment to clean workspace automatically:
            // cleanWs()
        }
    }
}
