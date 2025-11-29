pipeline {
    agent any

    environment {
        PROJECT_ID = 'resolute-bloom-476105-f9'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/praveen-goud999/appengine-aug.git'
            }
        }

        stage('Install Python 3.10 & Dependencies') {
            steps {
                script {
                    sh '''
                    echo "===== Installing Python 3.10 ====="
                    sudo apt-get update -y
                    sudo apt-get install -y python3.10 python3.10-venv python3.10-dev python3-pip

                    echo "===== Verify Python Version ====="
                    python3.10 --version

                    echo "===== Creating Python 3.10 Virtual Environment ====="
                    python3.10 -m venv venv

                    echo "===== Activating Virtualenv & Installing Dependencies ====="
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Validate app.yaml') {
            steps {
                sh '''
                echo "===== Current Directory ====="
                pwd

                echo "===== Listing Files ====="
                ls -l

                echo "===== app.yaml Content ====="
                cat app.yaml
                '''
            }
        }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    sh '''
                    echo "===== Activating Virtualenv ====="
                    . venv/bin/activate
                    python3 --version

                    echo "===== Authenticating with GCP ====="
                    gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                    gcloud config set project $PROJECT_ID

                    echo "===== Deploying App to App Engine ====="
                    export CLOUDSDK_PYTHON=python3.10
                    gcloud app deploy app.yaml --quiet --verbosity=info
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
