pipeline {
    agent any

    stages {
        stage('Pull Code') {
            steps {
                git 'https://github.com/<your-username>/<your-backend-repo>.git'
            }
        }

        stage('Set Permissions') {
            steps {
                sh 'sudo chmod -R 777 $WORKSPACE'
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                sh '''
                    python3 -m venv venv
                    source venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Start Flask App') {
            steps {
                sh '''
                    source venv/bin/activate
                    nohup python app.py --host=0.0.0.0 --port=5000 > flask.log 2>&1 &
                '''
            }
        }
    }
}
