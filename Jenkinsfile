pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                script {
                    bat './gradlew.bat test --tests "acceptation.DeterminantCalculatorFeature"'
                    bat './gradlew.bat test'
                }
            }
            post {
                always {
                    junit 'build/test-results/test/*.xml'
                    cucumber 'build/reports/cucumber/*.json'
                }
            }
        }
        stage('sonar') {
            steps {
                withSonarQubeEnv('sonar') {
                    bat './gradlew.bat sonar'
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
        stage('Build') {
            steps {
                script {
                    bat './gradlew.bat jar'
                    bat './gradlew.bat javadoc'
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                    archiveArtifacts artifacts: 'build/docs/javadoc/**/*', fingerprint: true
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    bat 'gradlew.bat publish'
                }
            }
        }
    }
    post {
        success {
            // Email Notification for Successful Deployment
            mail(
                to: 'lm_dabouz@esi.dz',
                subject: 'Deployment Success - Project mohamed',
                body: 'The deployment for the project mohamed was successful.'
            )

        }
        failure {

            // Email Notification for Pipeline Failure
            mail(
                to: 'lm_dabouz@esi.dz',
                subject: 'Pipeline Failed - Project mohamed',
                body: 'The Jenkins pipeline for project mohamed has failed. Please check the logs for more details.'
            )
        }
    }
}
