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
                sh "chown -R \$(whoami) $PROJECT_DIR"
                sh "chmod -R u+rwX $PROJECT_DIR"
            }
        }

        stage('Setup Python Env') {
            steps {
                dir("$PROJECT_DIR") {
                    sh '''
                        python3 -m venv venv
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
                    sh "fuser -k 5000/tcp || true"
                    // Using setsid for better daemonization
                    sh "setsid ./venv/bin/python app.py > flask.log 2>&1 < /dev/null &"
                }
            }
        }

        stage('Show Logs') {
            steps {
                dir("$PROJECT_DIR") {
                    sh "tail -n 20 flask.log"
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
            dir("${env.WORKSPACE}/webapp-backend") {
                sh "cat flask.log || true"
            }
        }
    }
}
