pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        } 
      stage('Unit Tests') {
            steps {
              # Running the unit tests.
              sh "mvn test"
            }
        } 
    }
}
