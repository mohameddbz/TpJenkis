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

        stage('Email Notification') {
                     steps {
                         script {
                             currentBuild.result = currentBuild.result ?: 'SUCCESS'
                             if (currentBuild.result == 'SUCCESS') {
                                 echo 'Sending success notifications...'
                                 mail to: 'lm_dabouz@esi.dz',
                                      subject: "Build Success: ",
                                      body: "The build and deployment for  was successful."
                             } else {
                                 echo 'Sending failure notifications...'
                                 mail to: 'lm_dabouz@esi.dz',
                                      subject: "Build Failed: ",
                                      body: "The build for  failed. Check the logs for details."
                             }


                         }
                     }
        }
        stage('Slack Notification') {
                     steps {
                         slackSend channel: '#work',
                                   color: 'good',
                                   message: "Build  completed successfully."
                     }
        }

    }

}
