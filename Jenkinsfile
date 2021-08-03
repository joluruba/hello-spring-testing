pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                echo 'Testing...'
                withGradle {
                    sh './gradlew clean test'
                }
            }
            post {
                always {
                    junit 'build/test-results/test/TEST-*.xml'
                    jacoco execPattern:'build/jacoco/*.exec'
                }
            }
        }
        stage ('QA') {
        	steps {
        		withGradle {
        			sh './gradlew check'
        		}
           	}
            post {
                always {
                recordIssues(
                    tools: [
                        pmdParser(pattern: 'build/reports/pmd/*.xml'),
                        spotBugs(pattern: 'build/reports/spotbugs/*.xml', useRankAsPriority: true)
                        ]
                    )
                }
            }
        }
        stage('Build') {
            steps {
                sh 'docker-compose build'
            }
        }
        stage('Security') {
             steps {
                 sh 'trivy image --format=jason --output=trivy-image.json hello-sping-testing:latest'
              }
              post {
                always{
                   recordIssues(
                      enabledForFailure: true,
                      aggregatingResults: true,
                      tool: trivy(pattern: 'trivy-*.json')
                      )
                  }
               }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
