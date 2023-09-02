pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        } 
      stage('Run Unit Tests') {
            steps {
              sh "mvn test"
            }
            post {
              always{
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
        } 
      stage ('Docker build and push'){
          steps {
            withDockerRegistry(credentialsId: 'docker', url: '') {
              sh 'printenv'
              sh 'docker build -t studymi/numeric-app:""$GIT_COMMIT"" .'
              sh 'docker push studymi/numeric-app:""$GIT_COMMIT""'
            }
          }
        }
    }
}
