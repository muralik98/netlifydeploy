pipeline {
    agent {
        docker {
            image 'python:3.8'  // Runs pipeline inside a Python 3.8 container
        }
    }

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-auth-token')
        NETLIFY_SITE_ID = 'nfp_NkqZ1QmsNVgf7W4r6znUWAe2zb2Cxg4ec402'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'apt-get update && apt-get install -y curl'
                sh 'python3 -m venv venv'
                sh './venv/bin/python -m pip install --upgrade pip'
                sh './venv/bin/pip install -r requirements.txt'
                sh 'curl -fsSL https://deb.nodesource.com/setup_16.x | bash -'
                sh 'apt-get install -y nodejs'
                sh 'npm install -g netlify-cli'
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
                sh 'netlify deploy --prod --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN'
            }
        }
    }
}
