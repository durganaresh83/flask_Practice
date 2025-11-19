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
                echo "Starting temporary MongoDB container for tests..."
                sh '''
                    docker run -d -p 27017:27017 --name test-mongo mongo:latest
                '''

                echo "Running unit tests..."
                sh '''
                    . venv/bin/activate
                    export MONGO_URI="mongodb://localhost:27017/testdb"
                    pytest
                '''

                echo "Stopping and removing MongoDB test container..."
                sh '''
                    docker stop test-mongo
                    docker rm test-mongo
                '''
            }
        }

        stage('Deploy') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo "Deploying Flask app to staging environment..."

                // Kill previous instance if it's running
                sh '''
                    pkill -f "python3 app.py" || true
                '''

                // Start new instance
                sh '''
                    . venv/bin/activate
                    nohup python3 app.py > app.log 2>&1 &
                '''
            }
        }

        stage('Health Check') {
            steps {
                echo "Performing post-deployment health check..."

                sh '''
                    sleep 5  # give Flask app time to start

                    STATUS=$(curl -o /dev/null -s -w "%{http_code}" http://localhost:5000)

                    if [ "$STATUS" -ne 200 ]; then
                        echo "❌ Health check failed. App returned HTTP $STATUS"
                        exit 1
                    else
                        echo "✅ Health check passed. Flask app is running successfully!"
                    fi
                '''
            }
        }

    }

    post {
        success {
            emailext(
                subject: "Build Successful: ${env.JOB_NAME}",
                body: "The Jenkins build was successful.",
                to: "durganareshpotta83@gmail.com"
            )
        }
        failure {
            emailext(
                subject: "Build Failed: ${env.JOB_NAME}",
                body: "The Jenkins build has failed.",
                to: "durganareshpotta83@gmail.com"
            )
        }
    }
}
