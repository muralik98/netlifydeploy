pipeline {
    agent { label 'docker-agent' }

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-auth-token')
        NETLIFY_SITE_ID = 'your-netlify-site-id'
    }

    stages {
        stage('Setup Python Environment') {
            steps {
                sh 'python3 -m venv venv'
                sh 'source venv/bin/activate && pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'source venv/bin/activate && pytest tests/'
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
                sh 'npx netlify deploy --prod --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN'
            }
        }
    }
}
