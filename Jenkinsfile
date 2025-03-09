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
                sh 'python3 -m ensurepip --default-pip'  // Ensure pip is installed
                sh 'python3 -m venv venv || virtualenv venv'  // Use venv or fallback to virtualenv
                sh './venv/bin/python -m pip install --upgrade pip'  // Upgrade pip
                sh './venv/bin/pip install -r requirements.txt'  // Install dependencies
            }
        }

        stage('Run Tests') {
            steps {
                sh './venv/bin/python -m pytest tests/'
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
                sh 'npm install -g netlify-cli'
                sh 'netlify deploy --prod --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN'
            }
        }
    }
}