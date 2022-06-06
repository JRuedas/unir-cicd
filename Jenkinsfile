pipeline {
    agent {
        label 'docker'
    }
    stages {
        stage('Source') {
            steps {
                //git 'https://github.com/srayuso/unir-cicd.git'
                git 'https://github.com/JRuedas/unir-test'
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
            publishHTML (target : [allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'results',
                reportFiles: '*_result.html',
                reportName: 'Test Reports',
                reportTitles: 'Test Reports'])
            cleanWs()
        }
        failure {
            emailext body: "Sending mail with body: Build $BUILD_NUMBER from job $JOB_NAME has failed.", subject: 'Jenkins build failed', to: 'developer@example.com'
            echo "Sending mail with body: Build $BUILD_NUMBER from job $JOB_NAME has failed."
        }
    }
}
