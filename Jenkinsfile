pipeline {
    agent any  // Runs directly on the Jenkins host
 
    options {
        skipDefaultCheckout(true) // Prevent Jenkins from auto-checking out code
    }
 
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to deploy')
    }
 
    environment {
        DATABASE_URL = credentials('attendance-db-url')
        SECRET_KEY = credentials('attendance-secret-key')
        PYTHONPATH = "/tmp/pip-packages/lib/python3.9/site-packages"
    }
 
    stages {
        stage('Checkout') {
            steps {
                sh 'git clone https://github.com/rishabhsrivastava05-eng/DevOps-Project.git .'
            }
        }
 
        stage('Build') {
            steps {
                sh 'ls -la' // Confirm requirements.txt is present
                sh 'python3 -m pip install --no-cache-dir --prefix=/tmp/pip-packages -r requirements.txt'
                sh 'python3 -m pip install --no-cache-dir --prefix=/tmp/pip-packages pytest'
            }
        }
 
        stage('Test') {
            steps {
                sh '''
                    export PYTHONPATH=/tmp/pip-packages
                    python3 -m pip install --target=/tmp/pip-packages pytest
                    python3 -m pytest tests/
                '''
            }
        }
 
        stage('Deploy') {
            steps {
                script {
                    if (params.BRANCH_NAME == 'main') {
                        sh 'docker compose build'
                        sh 'docker compose up -d'
                    } else if (params.BRANCH_NAME == 'staging') {
                        sh 'docker compose -f docker-compose.staging.yml build'
                        sh 'docker compose -f docker-compose.staging.yml up -d'
                    } else {
                        echo "No deployment configured for branch: ${params.BRANCH_NAME}"
                    }
                }
            }
        }
    }
 
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
