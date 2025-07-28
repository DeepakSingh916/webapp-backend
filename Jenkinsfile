pipeline {
    agent any

    environment {
        PROJECT_DIR = "${env.WORKSPACE}/webapp-backend"
        VENV_DIR = "${PROJECT_DIR}/venv"
        REPO_URL = 'https://github.com/DeepakSingh916/webapp-backend.git'
    }

    stages {
        stage('Cleanup') {
            steps {
                script {
                    // Agar directory exist karti hai toh ownership leke cleanup karo
                    sh """
                        if [ -d "$PROJECT_DIR" ]; then
                            sudo chown -R \$(whoami) $PROJECT_DIR || true
                            rm -rf $PROJECT_DIR
                        fi
                    """
                }
            }
        }

        stage('Clone Repo') {
            steps {
                sh "git clone $REPO_URL $PROJECT_DIR"
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
                    // Port 5000 pe chal raha app kill karo agar hai
                    sh "fuser -k 5000/tcp || true"
                    // App ko background me nohup se start karo
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
