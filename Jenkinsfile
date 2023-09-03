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
      stage('Mutation Tests - PIT'){
          steps {
              sh 'mvn org.pitest:pitest-maven:mutationCoverage'
          }
          post {
            always {
              pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
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
      stage ('Kubernetes Deployment - DevEnv'){
          steps {
            withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubeconfig', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
             sh "sed -i 's#replace#studymi/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml "
             sh "kubectl apply -f k8s_deployment_service.yaml"
            }  
          }
        }
    }
}
