pipeline {
    agent any

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-auth-token')
        NETLIFY_SITE_ID = 'e38c1dc2-8e53-4c5a-9e33-4951edbfa8eb'
    }

    stages {
        stage('Setup Python Environment') {
            steps {
                sh 'python3 -m venv venv'
                sh 'source venv/bin/activate && pip install -r requirements.txt'
            }
        }

        stage('Build Netlify Functions') {
            steps {
                sh 'cp -r app.py netlify/functions/'
            }
        }

        stage('Deploy to Netlify') {
            steps {
                sh 'npx netlify deploy --prod --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN'
            }
        }
    }
}
