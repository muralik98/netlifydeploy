pipeline {
    agent {
        docker {
            image 'python:3.8'  // Runs pipeline inside a Python 3.8 container
        }
    }

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-auth-token')
        NETLIFY_SITE_ID = 'e38c1dc2-8e53-4c5a-9e33-4951edbfa8eb'
        VENV_PATH = "venv"
    }

    stages {
        stage('Setup Python Environment') {
            steps {
                sh 'python -m ensurepip --default-pip'  // Ensure pip is installed
                sh 'python -m venv ${VENV_PATH}'  // Create virtual environment using the environment variable
                sh '. ${VENV_PATH}/bin/activate && pip install --upgrade pip'  // Activate venv and upgrade pip
                sh '. ${VENV_PATH}/bin/activate && pip install -r requirements.txt'  // Install dependencies in activated venv
            }
        }

        stage('Run Tests') {
            steps {
                sh '. ${VENV_PATH}/bin/activate && python -m pytest tests/'  // Run tests in activated venv
            }
        }

        stage('Prepare Netlify Functions') {
            steps {
                sh 'mkdir -p netlify/functions'
                sh 'cp app.py netlify/functions/'
            }
        }

        stage('Deploy to Netlify') {
            steps {
                sh 'npm install -g netlify-cli'  // Install Netlify CLI
                sh 'netlify deploy --prod --site=${NETLIFY_SITE_ID} --auth=${NETLIFY_AUTH_TOKEN}'  // Deploy to Netlify
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}