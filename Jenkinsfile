pipeline {
    agent {
        label 'docker'
    }
    stages {
        stage('Source') {
            steps {
                git 'https://github.com/JRuedas/unir-cicd'
            }
        }
        stage('Build') {
            steps {
                echo 'Building stage!'
                sh 'make build'
            }
        }
        stage('Unit tests') {
            steps {
                sh 'make test-unit'
                archiveArtifacts artifacts: 'results/*.xml'
                // Generacion de error para probar el envio del email cuando falla la pipeline.
                // error("Generating pipeline failure to test post email section.")
            }
        }
        stage('API tests') {
            steps {
                sh 'make test-api'
                archiveArtifacts artifacts: 'results/api_result.xml'
            }
        }
        stage('E2E tests') {
            steps {
                sh 'make test-e2e'
                archiveArtifacts artifacts: 'results/cypress_result.xml'
            }
        }        
    }
    post {
        always {
            junit 'results/*_result.xml'
            cleanWs()
        }
        failure {
            // mail to: "developer@example.com",
            // subject: "Jenkins build failed",
            // body: "Build $BUILD_NUMBER from job $JOB_NAME has failed."
            echo "Sending mail with body: Build $BUILD_NUMBER from job $JOB_NAME has failed."
        }
    }
}