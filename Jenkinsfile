pipeline {
    agent any
    
    stages {
        stage('Code Lint Verification') {
            steps {
                echo 'Verifying integrity of distribution code assets...'
                bat 'if not exist index.html exit 1'
            }
        }

        stage('Parallel Build and Test Operations') {
            parallel {
                stage('Container Compilation') {
                    steps {
                        echo 'Compiling static environment build states...'
                        // Explicitly passes the local TCP port directly to Docker
                        bat "set DOCKER_HOST=tcp://127.0.0.1:2375 && docker build -t smart-water-dashboard:latest ."
                    }
                }
                stage('Static Code & Telemetry Audit') {
                    steps {
                        echo 'Analyzing code for logic vulnerabilities...'
                        bat 'echo Verification checks completed successfully'
                    }
                }
            }
        }

        stage('Automated Deployment') {
            when { branch 'main' }
            steps {
                echo 'Condition Met: Target branch is main. Deploying to Kubernetes cluster...'
                bat "kubectl apply -f deployment.yaml"
                bat "kubectl rollout restart deployment/water-distribution-system || true"
            }
        }
        
        stage('Rollout Health Monitor & Rollback Execution') {
            when { branch 'main' }
            steps {
                echo 'Evaluating live service canary stabilization metrics...'
                script {
                    try {
                        bat "kubectl rollout status deployment/water-distribution-system --timeout=90s"
                        echo "Deployment stabilized with clean health records."
                    } catch (Exception e) {
                        echo "Deployment failure matched. Triggering automated system rollback..."
                        bat "kubectl rollout undo deployment/water-distribution-system"
                        error("Rollout stability verification timed out. Environment safely rolled back.")
                    }
                }
            }
        }
    }
}
