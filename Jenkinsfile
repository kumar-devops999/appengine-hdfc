pipeline {
    agent any
    environment {
        PROJECT_ID = 'resolute-bloom-476105-f9'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account-key')
        CLOUDSDK_PYTHON = 'python3'
    }
    stages {
        stage('Setup Python') {
            steps {
                echo "===== Setting up Python 3.11 ====="
                sh '''
                python3 --version
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Clean Workspace') {
            steps {
                echo "===== Cleaning old deployment artifacts ====="
                sh '''
                rm -rf .gcloudignore staging/
                '''
            }
        }

        stage('Authenticate GCP') {
            steps {
                echo "===== Authenticating with GCP ====="
                sh '''
                gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
                gcloud config set project $PROJECT_ID
                '''
            }
        }

        stage('Deploy to App Engine') {
            steps {
                echo "===== Deploying App Engine ====="
                sh '''
                . venv/bin/activate
                gcloud app deploy app.yaml --quiet --verbosity=info
                '''
            }
        }
    }
    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "Deployment succeeded!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
