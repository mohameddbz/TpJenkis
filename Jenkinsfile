pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                script {
                    bat 'gradlew.bat test --tests "acceptation.DeterminantCalculatorFeature"'
                    bat 'gradlew.bat test'
                }
            }
            post {
                always {
                    junit 'build\\test-results\\test\\*.xml'
                    cucumber 'build\\reports\\cucumber\\*.json'
                }
            }
        }
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                    bat 'gradlew.bat sonar'
                }
            }
        }
        stage('Code Quality') {
            steps {
                script {
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Pipeline failed due to Quality Gate failure: ${qualityGate.status}"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    bat 'gradlew.bat publish'
                }
            }
            post {
                success {
                    // Email Notification for Successful Deployment
                    mail(
                        to: 'faresmezenner@gmail.com',
                        subject: 'Deployment Success - Project mezenner-ci-cd',
                        body: 'The deployment for the project mezenner-ci-cd was successful.'
                    )

                    // Slack Notification for Successful Deployment
                    slackSend(
                        channel: '#tp-jinkins',
                        color: 'good',
                        message: 'Deployment succeeded for project mezenner-ci-cd!'
                    )
                }
                failure {
                    // Email Notification for Pipeline Failure
                    mail(
                        to: 'faresmezenner@gmail.com',
                        subject: 'Pipeline Failed - Project mezenner-ci-cd',
                        body: 'The Jenkins pipeline for project mezenner-ci-cd has failed. Please check the logs for more details.'
                    )

                    // Slack Notification for Pipeline Failure
                    slackSend(
                        channel: '#tp-jinkins',
                        color: 'danger',
                        message: 'Pipeline failed for project mezenner-ci-cd. Check Jenkins for details!'
                    )
                }
            }
        }
    }
}
