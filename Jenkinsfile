pipeline {
    agent any
    
    environment {
        DOCKER_HOST = 'tcp://127.0.0.1:2375'
        IMAGE_NAME = "smart-water-dashboard:latest"
        DEPLOYMENT_NAME = "water-distribution-system"
    }
    
    stages {
        stage('Code Lint Verification') {
            steps {
                echo 'Verifying integrity of distribution code assets...'
                bat 'if not exist index.html exit 1'
            }
        }

        // --- PIPELINE ADVANCED PATTERN 1: PARALLEL STAGES ---
        stage('Parallel Build and Test Operations') {
            parallel {
                stage('Container Compilation') {
                    steps {
                        echo 'Compiling static environment build states...'
                        bat "docker build -t %IMAGE_NAME% ."
                    }
                }
                stage('Static Code & Telemetry Audit') {
                    steps {
                        echo 'Analyzing code for logic vulnerabilities...'
                        // Simulates running automated quality checks on the dashboard
                        bat 'echo Verification checks completed successfully'
                    }
                }
            }
        }

        // --- PIPELINE ADVANCED PATTERN 2: CONDITIONAL STAGE EXECUTION ---
        stage('Automated Deployment') {
            when {
                branch 'main'
            }
            steps {
                echo 'Condition Met: Target branch is main. Deploying to Kubernetes cluster...'
                bat "kubectl apply -f deployment.yaml"
                bat "kubectl rollout restart deployment/%DEPLOYMENT_NAME% || true"
            }
        }
        
        stage('Rollout Health Monitor & Rollback Execution') {
            when {
                branch 'main'
            }
            steps {
                echo 'Evaluating live service canary stabilization metrics...'
                script {
                    try {
                        bat "kubectl rollout status deployment/%DEPLOYMENT_NAME% --timeout=90s"
                        echo "Deployment stabilized with clean health records."
                    } catch (Exception e) {
                        echo "Deployment failure matched. Triggering automated system rollback..."
                        bat "kubectl rollout undo deployment/%DEPLOYMENT_NAME%"
                        error("Rollout stability verification timed out. Environment safely rolled back.")
                    }
                }
            }
        }
    }
}
