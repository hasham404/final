pipeline {
    agent any
    
    environment {
        PYTHON_VERSION = 'python3'
        DEPLOYMENT_DIR = "${WORKSPACE}/deployment"
        PATH = "${HOME}/Library/Python/3.9/bin:${env.PATH}"  // Add pip user binaries to PATH
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
                    echo "Current branch: ${env.BRANCH_NAME}"
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

                    if [ -f "requirements.txt" ]; then
                        echo "Installing dependencies from requirements.txt..."
                        python3 -m pip install --upgrade pip --user
                        python3 -m pip install -r requirements.txt --user
                        echo "Dependencies installed successfully!"
                    else
                        echo "ERROR: requirements.txt not found!"
                        exit 1
                    fi

                    echo "Installed packages:"
                    python3 -m pip list --user
                '''
            }
        }
        
        stage('Run Unit Tests') {
            steps {
                echo '========================================'
                echo 'Stage 3: Running Unit Tests with pytest'
                echo '========================================'
                
                sh '''
                    export PATH="${HOME}/Library/Python/3.9/bin:$PATH"
                    echo "Running pytest..."
                    pytest test_skeleton_app.py -v --tb=short --junitxml=test-results.xml
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
                echo 'Stage 4: Building Application'
                echo '========================================'
                
                sh '''
                    BUILD_DIR="${WORKSPACE}/build"
                    mkdir -p ${BUILD_DIR}

                    echo "Packaging application..."
                    cp skeleton_app.py ${BUILD_DIR}/
                    cp requirements.txt ${BUILD_DIR}/

                    if [ -d "templates" ]; then
                        cp -r templates ${BUILD_DIR}/
                    fi

                    if [ -d "static" ]; then
                        cp -r static ${BUILD_DIR}/
                    fi

                    cd ${BUILD_DIR}
                    tar -czf flask-app-${BUILD_NUMBER}.tar.gz *
                    echo "Build package created: flask-app-${BUILD_NUMBER}.tar.gz"
                '''
            }
            
            post {
                always {
                    archiveArtifacts artifacts: 'build/flask-app-*.tar.gz', allowEmptyArchive: true
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo '========================================'
                echo 'Stage 5: Simulating Deployment'
                echo '========================================'
                
                sh '''
                    mkdir -p ${DEPLOYMENT_DIR}

                    echo "Deploying application to ${DEPLOYMENT_DIR}..."
                    cp skeleton_app.py ${DEPLOYMENT_DIR}/
                    cp requirements.txt ${DEPLOYMENT_DIR}/

                    if [ -d "templates" ]; then
                        cp -r templates ${DEPLOYMENT_DIR}/
                    fi

                    if [ -d "static" ]; then
                        cp -r static ${DEPLOYMENT_DIR}/
                    fi

                    cat > ${DEPLOYMENT_DIR}/deployment-info.txt << EOF
Deployment Information
=====================
Build Number: ${BUILD_NUMBER}
Build Date: $(date)
Git Commit: ${GIT_COMMIT}
Branch: ${BRANCH_NAME}
Deployed By: ${BUILD_USER}
EOF

                    echo "Deployment completed to ${DEPLOYMENT_DIR}"
                '''
            }
        }
    }
    
    post {
        always {
            echo '========================================'
            echo 'Pipeline Execution Summary'
            echo '========================================'
            echo "Build Number: ${BUILD_NUMBER}"
            echo "Status: ${currentBuild.currentResult}"
            echo "Duration: ${currentBuild.durationString}"
        }
        
        success {
            echo "Pipeline completed successfully! ✅"
        }
        
        failure {
            echo "Pipeline failed! ❌"
        }
    }
}
