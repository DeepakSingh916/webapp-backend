pipeline {
    agent any

    environment {
        PROJECT_DIR = "${env.WORKSPACE}/webapp-backend"
        VENV_DIR = "${PROJECT_DIR}/venv"
        REPO_URL = 'https://github.com/DeepakSingh916/webapp-backend.git'
    }

    stages {
        stage('Clone Repo') {
            steps {
                sh "rm -rf $PROJECT_DIR"
                sh "git clone $REPO_URL $PROJECT_DIR"
            }
        }

        stage('Setup Python Env') {
            steps {
                dir("$PROJECT_DIR") {
                    // Install virtualenv if not installed
                    sh 'python3 -m pip install --user virtualenv || true'
                    // Create venv if not exists
                    sh "[ -d venv ] || python3 -m virtualenv venv"
                    // Activate venv and install dependencies
                    sh '''
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install flask flask-cors
                    '''
                }
            }
        }

        stage('Run Flask App') {
            steps {
                dir("$PROJECT_DIR") {
                    // Kill any app running on port 5000
                    sh "fuser -k 5000/tcp || true"
                    // Run app in background with nohup, binding to 0.0.0.0
                    sh "nohup ./venv/bin/python app.py > flask.log 2>&1 &"
                }
            }
        }
    }

    post {
        success {
            echo 'Backend Flask app deployed successfully!'
        }
        failure {
            echo 'Backend deployment failed.'
        }
    }
}
