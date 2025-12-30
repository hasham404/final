pipeline {
    agent any
    
    environment {
        PYTHON_VERSION = 'python3'
        DEPLOYMENT_DIR = "${WORKSPACE}/deployment"
        VENV_DIR = "${WORKSPACE}/venv"
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
        
        stage('Setup Python Environment') {
            steps {
                echo '========================================'
                echo 'Stage 2: Setting up Python Environment'
                echo '========================================'
                
                sh '''
                    # Check Python version
                    ${PYTHON_VERSION} --version

                    # Create virtual environment if it doesn't exist
                    if [ ! -d "${VENV_DIR}" ]; then
                        echo "Creating virtual environment..."
                        ${PYTHON_VERSION} -m venv ${VENV_DIR}
                    fi

                    # Activate virtual environment and upgrade pip
                    source ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    echo "Python environment setup complete!"
                '''
            }
        }
        
        stage('Install Dependencies') {
            steps {
                echo '========================================'
                echo 'Stage 3: Installing Python Dependencies'
                echo '========================================'
                
                sh '''
                    source ${VENV_DIR}/bin/activate

                    if [ -f "requirements.txt" ]; then
                        echo "Installing dependencies from requirements.txt..."
                        pip install -r requirements.txt
                        echo "Dependencies installed successfully!"
                    else
                        echo "ERROR: requirements.txt not found!"
                        exit 1
                    fi

                    echo "Installed packages:"
                    pip list
                '''
            }
        }
        
        stage('Run Unit Tests') {
            steps {
                echo '========================================'
                echo 'Stage 4: Running Unit Tests with pytest'
                echo '========================================'
                
                sh '''
                    source ${VENV_DIR}/bin/activate
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
                echo 'Stage 5: Building Application'
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
                echo 'Stage 6: Simulating Deployment'
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

                    cat > ${DEPLOYMENT_DIR}/restart.sh << 'RESTART_EOF'
#!/bin/bash
echo "Stopping Flask application..."
# kill $(ps aux | grep '[s]keleton_app.py' | awk '{print $2}') 2>/dev/null || true
echo "Starting Flask application..."
# nohup python3 skeleton_app.py > app.log 2>&1 &
echo "Flask application restarted!"
RESTART_EOF

                    chmod +x ${DEPLOYMENT_DIR}/restart.sh
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
        
        unstable {
            echo "Pipeline is unstable! ⚠️"
        }
    }
}
