pipeline {
    agent any

    stages {

        stage('Build Info') {
            steps {
                echo "========================================="
                echo "Build Number : ${env.BUILD_NUMBER}"
                echo "Job Name     : ${env.JOB_NAME}"
                echo "Branch       : ${env.GIT_BRANCH}"
                echo "Commit       : ${env.GIT_COMMIT}"
                echo "Server       : ${env.NODE_NAME}"
                echo "Workspace    : ${env.WORKSPACE}"
                echo "========================================="
            }
        }

        stage('Checkout') {
            steps {
                echo "Pulling latest code from GitHub..."
                checkout scm
                echo "Code pulled successfully ✓"
            }
        }

        stage('Setup') {
            steps {
                echo "Checking Python version..."
                sh 'python3 --version'
                echo "Checking project structure..."
                sh 'find $WORKSPACE -name "*.py" | sort'
                echo "Creating virtual environment..."
                sh 'python3 -m venv venv'
                echo "Virtual environment created ✓"
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "Installing pytest and pyinstaller..."
                sh '''
                    . venv/bin/activate
                    pip install pytest pyinstaller
                    echo "Dependencies installed ✓"
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running Unit Tests..."
                sh '''
                    . venv/bin/activate
                    python3 -m pytest sources/test_calc.py -v
                '''
                echo "All Tests Passed ✓"
            }
        }

        stage('Build') {
            steps {
                echo "Building executable with PyInstaller..."
                sh '''
                    . venv/bin/activate
                    pyinstaller --onefile sources/add2vals.py
                '''
                echo "Executable built successfully ✓"
            }
        }

        stage('Archive') {
            steps {
                echo "Archiving build artifact..."
                archiveArtifacts artifacts: 'dist/add2vals', fingerprint: true
                echo "Artifact archived ✓"
            }
        }
    }

    post {
        success {
            echo "========================================="
            echo " All Stages Passed!"
            echo "BUILD SUCCESSFUL"
            echo "========================================="
        }
        failure {
            echo "========================================="
            echo " Build Failed! Check logs above."
            echo "========================================="
        }
        always {
            echo "Cleaning up virtual environment..."
            sh 'rm -rf venv'
            echo "Pipeline finished at: ${new Date()}"
        }
    }
}
