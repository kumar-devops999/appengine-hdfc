pipeline {
    agent any

    environment {
        PROJECT_ID = 'kiran400'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')  // Service account credential
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/praveen-goud999/appengine-aug.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Create and activate a virtual environment, then install dependencies
                    sh '''
                    # Update apt package list
                    sudo apt-get update -y
                    
                    # Install Python3 and development tools if not installed
                    sudo apt-get install -y python3-pip python3-dev python3-venv bash
                    
                    # Create a virtual environment
                    python3 -m venv venv
                    
                    # Activate the virtual environment using bash
                    . venv/bin/activate  # Using dot (.) instead of 'source'
                    
                    # Upgrade pip inside the virtual environment
                    pip install --upgrade pip
                    
                    # Install dependencies from requirements.txt
                    pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    // Ensure gcloud is installed and show working directory
                    sh 'gcloud --version'
                    sh 'pwd'  // Print the working directory
                    sh 'ls -l'  // List contents to ensure app.yaml is present

                    // Authenticate with Google Cloud using service account
                    sh 'gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}'

                    // Set the project ID for Google Cloud
                    sh 'gcloud config set project $PROJECT_ID'

                    // Deploy the application to App Engine
                    sh 'gcloud app deploy app.yaml --quiet'  // Quiet flag prevents interactive prompts
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            cleanWs()  // Optional: Clean workspace after the pipeline
        }

        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}
