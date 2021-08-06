pipeline {
    agent any

    stages {
      
            stage('Tests') {
            failFast true //con esto le decimos a que si una en paralelo falla no sigua 
            parallel {
        stage('Clean-test'){
 //           when { expression { false } } //con esto nos saltamos toda esta etapa
            steps {
              echo 'Testing...'
                withGradle {
                    sh './gradlew clean test '
                }
              }
        }
                stage('test-pitest'){
 //           when { expression { false } } //con esto nos saltamos toda esta etapa
            steps {
              echo 'Testing pitest'
                withGradle {
                    sh './gradlew pitest'
                }
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
      }
    stage('Analisis') {
            failFast true //con esto le decimos a que si una en paralelo falla no sigua 
            parallel { //con esto ejecutaremos las 2 fases de Analisis (sonar y QA) en parelelo

    stage('SonarQube Analysis') {
        when { expression { false } } //con esta linea se salta el analisis de sonarqube
        steps {
          withSonarQubeEnv('local') {
           sh './gradlew sonarqube'
        }
       }
     }
    stage ('QA') {
//        when { expression { false } }  //con esto nos saltamos toda esta etapa para acelerar
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
    stage('Build-Publish') {
          steps {

            sh 'docker build . -t 10.250.7.3:5050/joluruba/hello-spring-testing:latest -t 10.250.7.3:5050/joluruba/hello-spring-testing:1.0.${BUILD_NUMBER}'
            withDockerRegistry([url:'http://10.250.7.3:5050', credentialsId:'jenkins-registry2']){
              sh 'docker push --all-tags 10.250.7.3:5050/joluruba/hello-spring-testing'
            }
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
