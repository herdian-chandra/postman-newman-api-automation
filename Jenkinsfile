pipeline {
    agent any

    tools {
        nodejs 'nodejs-24'
    }

    parameters {
        choice(name: 'TARGET_ENV', choices: ['DEVELOPMENT', 'STAGING'], description: 'Select target environment')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Show Context') {
            steps {
                echo "Branch: ${env.BRANCH_NAME}"
                echo "Selected environment: ${params.TARGET_ENV}"
                sh 'node -v'
                sh 'npm -v'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Prepare Reports Folder') {
            steps {
                sh 'mkdir -p reports'
            }
        }

        stage('Run Newman') {
            steps {
                script {
                    def envFile = params.TARGET_ENV == 'STAGING'
                        ? 'environments/STAGING.postman_environment.json'
                        : 'environments/DEVELOPMENT.postman_environment.json'

                    sh """
                        npx newman run collections/Automation-Restful-Booker.postman_collection.json \
                        -e ${envFile} \
                        -r cli,htmlextra,json \
                        --reporter-htmlextra-export reports/automation-restful-booker-report.html \
                        --reporter-json-export reports/newman-result.json
                    """
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/*', fingerprint: true
        }
        success {
            echo 'Newman execution completed successfully.'
        }
        failure {
            echo 'Newman execution failed.'
        }
    }
}