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
                sh 'rm -rf $WORKSPACE/webapp-backend'
                sh "git clone $REPO_URL $PROJECT_DIR"
            }
        }

        stage('Set Permissions') {
            steps {
                sh 'sudo chmod -R 777 $WORKSPACE'
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
                    // Kill port 5000 more forcefully
                    sh "sudo fuser -k 5000/tcp || true"
                    // Start app
                    sh "nohup ./venv/bin/python app.py > flask.log 2>&1 &"
                }
            }
        }
    }

    post {
        success {
            echo '✅ Backend Flask app deployed successfully!'
        }
        failure {
            echo '❌ Backend deployment failed.'
        }
    }
}
