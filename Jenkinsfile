pipeline {
    agent any

    environment {
        GIT_SSH_COMMAND = 'ssh -o StrictHostKeyChecking=no'
    }

    triggers {
        githubPush() // This listens for GitHub push events
    }

    stages {
        stage('Clean Workspace') {
            steps {
                sh 'rm -rf deep-learning-flask-api'
            }
        }

        stage('Clone Repository') {
            steps {
                git credentialsId: 'github_ssh', url: 'git@github.com:hussainsiddik/deep-learning-flask-api.git', branch: 'main'
            }
        }

        stage('Install Python Dependencies') {
            steps {
                sh '''
                    cd deep-learning-flask-api
                    /opt/venv/bin/pip install -r requirements.txt || true
                '''
            }
        }

        stage('Run Dummy Tests') {
            steps {
                sh '''
                    cd deep-learning-flask-api
                    echo "def test_sample(): assert True" > test_sample.py
                    /opt/venv/bin/pytest test_sample.py --html=report.html || true
                '''
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'deep-learning-flask-api',
                    reportFiles: 'report.html',
                    reportName: 'Test Report'
                ])
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully."
        }
        failure {
            echo "❌ Pipeline failed. Please check logs."
        }
    }
}
