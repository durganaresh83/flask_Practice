pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Creating virtual environment and installing dependencies..."
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh '''
                    . venv/bin/activate
                    pytest
                '''
            }
        }

        stage('Deploy') {
            when {
                expression {
                    currentBuild.result == null || currentBuild.result == 'SUCCESS'
                }
            }
            steps {
                echo "Deploying Flask app to staging environment..."
                sh '''
                    . venv/bin/activate
                    nohup python3 app.py > app.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            emailext(
                subject: "Build Successful: ${env.JOB_NAME}",
                body: "The Jenkins build was successful.",
                recipientProviders: [developers()]
            )
        }
        failure {
            emailext(
                subject: "Build Failed: ${env.JOB_NAME}",
                body: "The Jenkins build has failed.",
                recipientProviders: [developers()]
            )
        }
    }
}
