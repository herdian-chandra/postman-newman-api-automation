pipeline {
    agent any

    tools {
        nodejs 'nodejs'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run API Automation') {
            steps {
                sh 'chmod +x scripts/run-newman.sh'
                sh './scripts/run-newman.sh'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/*.html', fingerprint: true
        }
        success {
            echo 'API automation executed successfully.'
        }
        failure {
            echo 'API automation execution failed.'
        }
    }
}