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
                // Use the SonarQube environment wrapper
                withSonarQubeEnv('sonar') { // Replace 'SonarQube' with the name of your configured SonarQube server in Jenkins
                    bat './gradlew.bat sonar'
                }

            }
        }

        stage('Code Quality') {
             steps {
                 script {
                     def qualityGate = waitForQualityGate() // Wait for SonarQube's analysis result
                     if (qualityGate.status != 'OK') {
                         error "Pipeline failed due to Quality Gate failure: ${qualityGate.status}"
                     }
                 }
             }
        }
        stage('Build') {
            steps {
                script {
                    // Generate the JAR file
                    bat './gradlew.bat jar'

                    // Generate the documentation
                    bat './gradlew.bat javadoc'
                }
            }
            post {
                success {
                    // Archive the generated JAR and documentation
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
}