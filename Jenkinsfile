pipeline {
    agent any

    environment {
        AWS_SSH_KEY = credentials('ssh_key')  // Replace with your credential ID
        EC2_USER = 'ubuntu'
        EC2_IP = '3.110.47.134'
        APP_DIR = '/home/ubuntu/app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/PAVANHN18/learn-jenkins.git'
            }
        }

        stage('Setup Python') {
            steps {
                sh 'python3 -m venv venv && source venv/bin/activate'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest tests/'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['aws-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} <<EOF
                    sudo apt update
                    sudo apt install python3-venv -y
                    mkdir -p ${APP_DIR}
                    EOF
                    
                    scp -o StrictHostKeyChecking=no -r * ${EC2_USER}@${EC2_IP}:${APP_DIR}

                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} <<EOF
                    cd ${APP_DIR}
                    python3 -m venv venv
                    source venv/bin/activate
                    pip install -r requirements.txt
                    nohup python3 app.py > output.log 2>&1 &
                    EOF
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}

