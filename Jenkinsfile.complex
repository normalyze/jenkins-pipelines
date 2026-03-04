pipeline {
    agent any
    parameters {
        string(name: 'PIPELINE_TYPE', defaultValue: 'cleanup', description: 'Type of pipeline to run: cleanup, deploy, security-scan, build-worker')
        string(name: 'ENVIRONMENT', defaultValue: 'dev', description: 'Target environment')
        booleanParam(name: 'DRY_RUN', defaultValue: true, description: 'Dry run only')
    }
    stages {
        stage('Select Pipeline') {
            steps {
                script {
                    echo "Selected pipeline: ${params.PIPELINE_TYPE}"
                    echo "Environment: ${params.ENVIRONMENT}"
                    echo "Dry run: ${params.DRY_RUN}"
                    
                    switch(params.PIPELINE_TYPE.toLowerCase()) {
                        case 'cleanup':
                            echo "Running cleanup pipeline..."
                            sh 'echo "Cleanup operations would run here"'
                            break
                        case 'deploy':
                            echo "Running deploy pipeline..."
                            sh 'echo "Deploy operations would run here"'
                            break
                        case 'security-scan':
                            echo "Running security scan pipeline..."
                            sh 'echo "Security scan operations would run here"'
                            break
                        case 'build-worker':
                            echo "Running build-worker pipeline..."
                            sh 'echo "Build-worker operations would run here"'
                            break
                        default:
                            error "Unknown pipeline type: ${params.PIPELINE_TYPE}"
                    }
                }
            }
        }
    }
    post {
        always {
            echo "Pipeline execution completed"
        }
        success {
            echo "✅ Pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
