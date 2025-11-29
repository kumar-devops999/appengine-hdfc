pipeline {
    agent any

    environment {
        PROJECT_ID = 'resolute-bloom-476105-f9'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
    }

    stages {

        stage('Clean workspace') {
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
                    sudo apt-get install -y software-properties-common
                    sudo add-apt-repository ppa:deadsnakes/ppa -y
                    sudo apt-get update -y
                    sudo apt-get install -y python3.10 python3.10-venv python3.10-dev

                    echo "===== Python Versions Installed ====="
                    python3 --version || true
                    python3.10 --version || true

                    echo "===== Creating Python 3.10 Virtual Environment ====="
                    python3.10 -m venv venv

                    echo "===== Activating Virtualenv & Installing Requirements ====="
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
                echo "===== PWD ====="
                pwd

                echo "===== LISTING PROJECT FILES ====="
                ls -R

                echo "===== app.yaml CONTENT ====="
                cat app.yaml
                '''
            }
        }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    sh '''
                    echo "===== Using Python inside Virtualenv ====="
                    . venv/bin/activate
                    python3 --version

                    echo "===== Authenticating with GCP ====="
                    gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                    gcloud config set project $PROJECT_ID

                    echo "===== Deploying to App Engine ====="
                    . venv/bin/activate
                    export CLOUDSDK_PYTHON=python3.10
                    gcloud app deploy app.yaml --quiet --verbosity=info
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
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
