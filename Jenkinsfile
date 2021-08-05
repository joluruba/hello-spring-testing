pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
              echo 'Testing...'
                withGradle {
                    sh './gradlew clean test pitest'
                }
              }
             post {
                always {
                    junit 'build/test-results/test/TEST-*.xml'
                    jacoco execPattern:'build/jacoco/*.exec'
                    recordIssues(enabledForFailure: true, tool: pit(pattern:"build/reports/pitest/**/*.xml"))
                }
            }
        }
    stage('Analisis') {
            failFast true //con esto le decimos a que si una en paralelo falla no sigua 
            parallel { //con esto ejecutaremos las 2 fases de Analisis (sonar y QA) en parelelo

    stage('SonarQube Analysis') {
      // con esta liena se saltará sonarqube->  when { expression { false } }
        steps {
          withSonarQubeEnv('local') {
           sh './gradlew sonarqube'
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
 }
}
    stage('Build') {
          steps {
            sh 'docker-compose build'
           }
    }
    stage('Security') {
        steps {
          sh 'trivy image --format=json --output=trivy-image.json hellospring:latest'
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
