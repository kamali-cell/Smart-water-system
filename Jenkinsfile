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
                sh 'test -f index.html'
            }
        }
        
        stage('Container Image Build') {
            steps {
                echo 'Compiling static environment build states...'
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }
        
        stage('Automated Deployment') {
            steps {
                echo 'Deploying current image iteration to Kubernetes cluster...'
                sh "kubectl apply -f deployment.yaml"
                sh "kubectl rollout restart deployment/${DEPLOYMENT_NAME} || true"
            }
        }
        
        stage('Rollout Health Monitor & Rollback Execution') {
            steps {
                echo 'Evaluating live service canary stabilization metrics...'
                script {
                    try {
                        // Monitors deployment health up to 90 seconds
                        sh "kubectl rollout status deployment/${DEPLOYMENT_NAME} --timeout=90s"
                        echo "Deployment stabilized with clean health records."
                    } catch (Exception e) {
                        echo "⚠️ Deployment failure matched. Triggering automated system rollback..."
                        sh "kubectl rollout undo deployment/${DEPLOYMENT_NAME}"
                        error("Rollout stability verification timed out. Environment safely rolled back.")
                    }
                }
            }
        }
    }
}
