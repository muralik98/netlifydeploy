pipeline {
    agent any

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-auth-token')
        NETLIFY_SITE_ID = 'e38c1dc2-8e53-4c5a-9e33-4951edbfa8eb'
        VENV_PATH = "venv"
    }

    stages {
        stage('Install Python Venv Dependencies') {
            steps {
                script {
                    sh """
                    sudo apt update
                    sudo apt install -y python3-venv
                    """
                }
            }
        }

        stage('Setup Python Environment') {
            steps {
                script {
                    sh """
                    python3 -m venv ${VENV_PATH}
                    source ${VENV_PATH}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    """
                }
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

    post {
        success {
            echo "Deployment to Netlify successful!"
        }
        failure {
            echo "Deployment failed. Check logs."
        }
    }
}
