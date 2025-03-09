pipeline {
    agent {
        docker {
            image 'python:3.8'  // Runs pipeline inside a Python 3.8 container
        }
    }

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-auth-token')
        NETLIFY_SITE_ID = 'e38c1dc2-8e53-4c5a-9e33-4951edbfa8eb'
    }

    stages {
        stage('Setup Python Environment') {
            steps {
                // Install dependencies directly without virtual environment
                sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
                // Ignore the pip as root warning as it's expected in Docker
            }
        }

        stage('Run Tests') {
            steps {
                sh 'python -m pytest tests/'
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
                sh 'netlify deploy --prod --site=${NETLIFY_SITE_ID} --auth=${NETLIFY_AUTH_TOKEN}'
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}