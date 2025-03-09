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
            }
        }



        stage('Prepare Netlify Functions') {
            steps {
                sh 'mkdir -p netlify/functions'
                // Copy all files and directories to the Netlify functions directory
                sh 'cp -r * netlify/functions/'
                // Exclude the netlify directory itself to avoid recursion
                sh 'rm -rf netlify/functions/netlify'
                // List contents to verify
                sh 'ls -la netlify/functions'
            }
        }

        stage('Install Node.js and Netlify CLI') {
            steps {
                // Install Node.js and npm in the Python container
                sh 'apt-get update && apt-get install -y curl'
                sh 'curl -fsSL https://deb.nodesource.com/setup_16.x | bash -'
                sh 'apt-get install -y nodejs'
                // Verify installations
                sh 'node --version'
                sh 'npm --version'
                // Install Netlify CLI
                sh 'npm install -g netlify-cli'
            }
        }

        stage('Deploy to Netlify') {
            steps {
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