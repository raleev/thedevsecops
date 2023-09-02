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
        } 
    }
}
