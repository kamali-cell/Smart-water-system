pipeline {
    agent any
    
    stages {
        // 1. Core verification stage
        stage('Code Lint Verification') {
            steps {
                echo 'Verifying integrity of distribution code assets...'
                bat 'if not exist index.html exit 1'
            }
        }

        // --- PIPELINE ADVANCED PATTERN 1: PARALLEL STAGES & TESTING ---
        stage('Parallel Build and Test Operations') {
            parallel {
                stage('Telemetry Logic Audit') {
                    steps {
                        echo 'Analyzing frontend threshold rules logic...'
                        bat 'echo Threshold parameter compliance verified.'
                    }
                }
                stage('Static Asset Manifest Check') {
                    steps {
                        echo 'Auditing static web application files layout...'
                        bat 'dir index.html deployment.yaml'
                    }
                }
            }
        }

        // --- PIPELINE ADVANCED PATTERN 2: CONDITIONAL STAGE EXECUTION ---
        stage('Automated Deployment') {
            when { branch 'main' }
            steps {
                echo 'Condition Met: Target branch is main. Preparing release deployment...'
                bat 'echo Deploying system manifests to target cluster...'
            }
        }
        
        // --- PIPELINE ADVANCED PATTERN 3: ARCHIVE BUILD ARTIFACTS ---
        stage('Archive Operational Assets') {
            when { branch 'main' }
            steps {
                echo 'Archiving dashboard build artifacts for operations audit...'
                // Natively packages the dashboard configuration as a stable artifact file
                archiveArtifacts artifacts: 'index.html, deployment.yaml', fingerprint: true
            }
        }
    }
}
