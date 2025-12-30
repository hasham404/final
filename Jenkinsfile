pipeline {
    agent any

    environment {
        // Add user's local Python bin to PATH
        PATH = "/Users/hasham/Library/Python/3.9/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo '========================================'
                echo 'Stage 1: Cloning Repository from GitHub'
                echo '========================================'
                checkout scm
                script {
                    echo "Repository cloned successfully!"
                    echo "Current branch: ${env.GIT_BRANCH}"
                    echo "Commit: ${env.GIT_COMMIT}"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '========================================'
                echo 'Stage 2: Installing Python Dependencies'
                echo '========================================'
                sh '''
                    echo "Python version:"
                    python3 --version
                    if [ -f requirements.txt ]; then
                        echo "Installing dependencies from requirements.txt..."
                        python3 -m pip install --upgrade pip --user
                        python3 -m pip install -r requirements.txt --user
                        echo "Dependencies installed successfully!"
                        echo "Installed packages:"
                        python3 -m pip list --user
                    else
                        echo "requirements.txt not found, skipping dependency installation."
                    fi
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo '========================================'
                echo 'Stage 3: Running Unit Tests with pytest'
                echo '========================================'
                sh '''
                    echo "Running pytest..."
                    pytest -v --tb=short --junitxml=test-results.xml
                '''
            }
            post {
                always {
                    junit 'test-results.xml'
                    archiveArtifacts artifacts: 'test-results.xml', allowEmptyArchive: true
                }
            }
        }

        stage('Build') {
            steps {
                echo '========================================'
                echo 'Stage 4: Build Stage (Optional)'
                echo '========================================'
                // Add your build steps here if needed
            }
        }

        stage('Deploy') {
            steps {
                echo '========================================'
                echo 'Stage 5: Deploy Stage (Optional)'
                echo '========================================'
                // Add your deployment steps here if needed
            }
        }
    }

    post {
        always {
            echo '========================================'
            echo 'Pipeline Execution Summary'
            echo '========================================'
            echo "Build Number: ${env.BUILD_NUMBER}"
            echo "Status: ${currentBuild.currentResult}"
            echo "Duration: ${currentBuild.durationString}"
        }
    }
}
