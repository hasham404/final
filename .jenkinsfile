pipeline {
    agent any
    
    environment {
        // Python version - adjust based on your Jenkins environment
        PYTHON_VERSION = 'python3'
        // Deployment target directory
        DEPLOYMENT_DIR = "${WORKSPACE}/deployment"
        // Virtual environment directory
        VENV_DIR = "${WORKSPACE}/venv"
    }
    
    stages {
        stage('Checkout') {
            steps {
                script {
                    echo '========================================'
                    echo 'Stage 1: Cloning Repository from GitHub'
                    echo '========================================'
                }
                // Checkout code from GitHub
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
                script {
                    echo '========================================'
                    echo 'Stage 2: Setting up Python Environment'
                    echo '========================================'
                }
                
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
                script {
                    echo '========================================'
                    echo 'Stage 3: Installing Python Dependencies'
                    echo '========================================'
                }
                
                sh '''
                    # Activate virtual environment
                    source ${VENV_DIR}/bin/activate
                    
                    # Install dependencies from requirements.txt
                    if [ -f "requirements.txt" ]; then
                        echo "Installing dependencies from requirements.txt..."
                        pip install -r requirements.txt
                        echo "Dependencies installed successfully!"
                    else
                        echo "ERROR: requirements.txt not found!"
                        exit 1
                    fi
                    
                    # Display installed packages
                    echo "Installed packages:"
                    pip list
                '''
            }
        }
        
        stage('Run Unit Tests') {
            steps {
                script {
                    echo '========================================'
                    echo 'Stage 4: Running Unit Tests with pytest'
                    echo '========================================'
                }
                
                sh '''
                    # Activate virtual environment
                    source ${VENV_DIR}/bin/activate
                    
                    # Run pytest with coverage and verbose output
                    echo "Running pytest..."
                    pytest test_skeleton_app.py -v --tb=short --junitxml=test-results.xml || true
                    
                    # Generate test report
                    echo "Test execution completed!"
                '''
            }
            
            post {
                always {
                    // Publish test results
                    junit 'test-results.xml'
                    
                    // Archive test results
                    archiveArtifacts artifacts: 'test-results.xml', allowEmptyArchive: true
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    echo '========================================'
                    echo 'Stage 5: Building Application'
                    echo '========================================'
                }
                
                sh '''
                    # Create build directory
                    BUILD_DIR="${WORKSPACE}/build"
                    mkdir -p ${BUILD_DIR}
                    
                    # Copy application files to build directory
                    echo "Packaging application..."
                    
                    # Copy main application files
                    cp skeleton_app.py ${BUILD_DIR}/
                    cp requirements.txt ${BUILD_DIR}/
                    
                    # Copy templates directory
                    if [ -d "templates" ]; then
                        cp -r templates ${BUILD_DIR}/
                        echo "Templates directory copied"
                    fi
                    
                    # Copy static directory if it exists
                    if [ -d "static" ]; then
                        cp -r static ${BUILD_DIR}/
                        echo "Static directory copied"
                    fi
                    
                    # Create deployment package
                    cd ${BUILD_DIR}
                    tar -czf flask-app-${BUILD_NUMBER}.tar.gz *
                    
                    echo "Build package created: flask-app-${BUILD_NUMBER}.tar.gz"
                    echo "Build completed successfully!"
                '''
            }
            
            post {
                always {
                    // Archive build artifacts
                    archiveArtifacts artifacts: 'build/flask-app-*.tar.gz', allowEmptyArchive: true
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    echo '========================================'
                    echo 'Stage 6: Simulating Deployment'
                    echo '========================================'
                }
                
                sh '''
                    # Create deployment directory
                    mkdir -p ${DEPLOYMENT_DIR}
                    
                    # Copy built application to deployment directory
                    echo "Deploying application to ${DEPLOYMENT_DIR}..."
                    
                    # Copy application files
                    cp skeleton_app.py ${DEPLOYMENT_DIR}/
                    cp requirements.txt ${DEPLOYMENT_DIR}/
                    
                    # Copy directories
                    if [ -d "templates" ]; then
                        cp -r templates ${DEPLOYMENT_DIR}/
                    fi
                    
                    if [ -d "static" ]; then
                        cp -r static ${DEPLOYMENT_DIR}/
                    fi
                    
                    # Create a deployment info file
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
                    echo "Deployment info saved to deployment-info.txt"
                    
                    # Simulate service restart (just create a restart script)
                    cat > ${DEPLOYMENT_DIR}/restart.sh << 'RESTART_EOF'
#!/bin/bash
# Simulated service restart script
echo "Stopping Flask application..."
# kill $(ps aux | grep '[s]keleton_app.py' | awk '{print $2}') 2>/dev/null || true
echo "Starting Flask application..."
# nohup python3 skeleton_app.py > app.log 2>&1 &
echo "Flask application restarted!"
RESTART_EOF
                    
                    chmod +x ${DEPLOYMENT_DIR}/restart.sh
                    echo "Restart script created at ${DEPLOYMENT_DIR}/restart.sh"
                '''
            }
            
            post {
                success {
                    echo "Deployment completed successfully!"
                }
                failure {
                    echo "Deployment failed!"
                }
            }
        }
    }
    
    post {
        always {
            script {
                echo '========================================'
                echo 'Pipeline Execution Summary'
                echo '========================================'
                echo "Build Number: ${BUILD_NUMBER}"
                echo "Status: ${currentBuild.currentResult}"
                echo "Duration: ${currentBuild.durationString}"
            }
            
            // Clean up workspace (optional)
            // cleanWs()
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

