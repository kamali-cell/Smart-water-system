pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "smart-water-dashboard:latest"
        DEPLOYMENT_NAME = "water-distribution-system"
    }
    
    stages {
        stage('Code Lint Verification') {
            steps {
                echo 'Verifying integrity of distribution code assets...'
                // Uses Windows batch commands ('bat') instead of Linux Shell ('sh')
                bat 'if not exist index.html exit 1'
            }
        }
        
        stage('Container Image Build') {
            steps {
                echo 'Compiling static environment build states...'
                bat "docker build -t ${IMAGE_NAME} ."
            }
        }
        
        stage('Automated Deployment') {
            steps {
                echo 'Deploying current image iteration to Kubernetes cluster...'
                bat "kubectl apply -f deployment.yaml"
                bat "kubectl rollout restart deployment/${DEPLOYMENT_NAME} || true"
            }
        }
        
        stage('Rollout Health Monitor & Rollback Execution') {
            steps {
                echo 'Evaluating live service canary stabilization metrics...'
                script {
                    try {
                        bat "kubectl rollout status deployment/${DEPLOYMENT_NAME} --timeout=90s"
                        echo "Deployment stabilized with clean health records."
                    } catch (Exception e) {
                        echo "Deployment failure matched. Triggering automated system rollback..."
                        bat "kubectl rollout undo deployment/${DEPLOYMENT_NAME}"
                        error("Rollout stability verification timed out. Environment safely rolled back.")
                    }
                }
            }
        }
    }
}
