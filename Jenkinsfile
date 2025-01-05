pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                script {
                    // Run tests
                    bat 'gradlew.bat test --tests "acceptation.DeterminantCalculatorFeature"'
                    bat 'gradlew.bat test'
                }
            }
            post {
                always {
                    // Publish JUnit test results
                    junit 'build\\test-results\\test\\*.xml'

                    // Publish Cucumber JSON reports
                    cucumber 'build\\reports\\cucumber\\*.json'
                }
            }
        }
        stage('SonarQube') {
            steps {
                // Use the SonarQube environment wrapper
                withSonarQubeEnv('sonar') { // Replace 'sonar' with your SonarQube configuration name in Jenkins
                    bat 'gradlew.bat sonar'
                }
            }
        }
        stage('Code Quality') {
            steps {
                script {
                    // Wait for SonarQube Quality Gate result
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Pipeline failed due to Quality Gate failure: ${qualityGate.status}"
                    }
                }
            }
        }
    }
}
