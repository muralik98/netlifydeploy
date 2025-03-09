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


        stage('Prepare Netlify Structure') {
            steps {
                // Create proper Netlify directory structure
                sh 'mkdir -p netlify/functions'

                // Create a netlify.toml file for configuration
                sh '''
                cat > netlify.toml << 'EOL'
[build]
  command = "pip install -r requirements.txt"
  functions = "netlify/functions"
  publish = "public"

[[redirects]]
  from = "/*"
  to = "/.netlify/functions/flask_app"
  status = 200
EOL
                '''

                // Create a public directory for static files
                sh 'mkdir -p public'
                sh 'cp -r templates/static/* public/ || true'  // Copy static files if they exist

                // Create a Flask app wrapper as a Netlify function
                sh '''
                cat > netlify/functions/flask_app.py << 'EOL'
from flask_serverless import FlaskServerless
import sys, os

# Add the current directory to the path so that we can import the app
sys.path.append(os.path.dirname(os.path.dirname(os.path.dirname(__file__))))

# Import the Flask app
from app import app

# Create a serverless handler
handler = FlaskServerless(app).handler
EOL
                '''

                // Create a requirements.txt file for the function
                sh '''
                cat > netlify/functions/requirements.txt << 'EOL'
flask
flask-serverless
EOL
                '''

                // Copy the main app and other necessary files
                sh 'cp -r app.py requirements.txt templates netlify/functions/'
                sh 'ls -la netlify/functions'
            }
        }

        stage('Install Node.js and Netlify CLI') {
            steps {
                // Install Node.js and npm in the Python container
                sh 'apt-get update && apt-get install -y curl'
                sh 'curl -fsSL https://deb.nodesource.com/setup_16.x | bash -'
                sh 'apt-get install -y nodejs'
                // Install Netlify CLI
                sh 'npm install -g netlify-cli'
            }
        }

        stage('Deploy to Netlify') {
            steps {
                // Deploy with the Netlify CLI
                sh 'netlify deploy --prod --dir=. --site=${NETLIFY_SITE_ID} --auth=${NETLIFY_AUTH_TOKEN}'
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}