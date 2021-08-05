pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
              sh 'docker build -t 10.250.7.3:5050/joluruba/hello-spring-testing:latest -t 10.250.7.3:5050/joluruba/hello-spring-testing:1.0.${BUILD_NUMBER}'
        }
    }
    docker.withRegistry('10.250.7.3:5050', 'jenkins-registry') {

        // def customImage = docker.build("my-image:${env.BUILD_ID}")
        // sh 'docker login 10.250.7.3:5050 -u joluruba'
        sh 'docker push --all-tags 10.250.7.3:5050/joluruba/hello-spring-testing:latest'
        
        // customImage.push()
    }

    stage('Deploy') {
       steps {
          echo 'Deploying...'
            }
        }
    }
}
