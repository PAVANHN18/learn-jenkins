pipeline {
    agent any

    environment {
        AWS_SSH_KEY = credentials('ssh_key')  // Ensure 'ssh_key' is the correct Jenkins credential ID
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
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate  # Use '.' instead of 'source' for compatibility
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest tests/
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ssh_key']) {  // Ensure 'ssh_key' matches Jenkins credentials
                    sh '''
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} << 'EOF'
                        set -e  # Exit on error
                        sudo apt update
                        sudo apt install -y py
