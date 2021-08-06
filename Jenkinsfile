pipeline {
    agent any

    stages {
 //       stage('Build') {
 //           steps {
 //           
 //       }
 //   }
    stage('Build-Push') {
        steps {
         withDockerRegistry([url:'10.250.7.3:5050', credentialsId:'jenkins-registry2']) {
         sh 'docker build . -t 10.250.7.3:5050/joluruba/hello-spring-testing:latest -t 10.250.7.3:5050/joluruba/hello-spring-testing:1.0.${BUILD_NUMBER}'
         //customImage = docker.build("10.250.7.3:5050/joluruba/hello-spring-testing:1.0.${BUILD_NUMBER}")
        // sh 'docker login 10.250.7.3:5050 -u joluruba'
         sh 'docker push --all-tags 10.250.7.3:5050/joluruba/hello-spring-testing:latest'
     
        //customImage.push()
        }
      }
     }
    }
}
