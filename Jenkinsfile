// Jenkins Pipeline
pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archiveArtifacts 'target/*.jar'
            }
        } 
      stage('Run Unit Tests') {
            steps {
              sh "mvn test"
            }
            post {
              always{
		// Running junit and jacoco
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
      }
      stage('Mutation Tests - PIT-Test'){
          steps {
              sh 'mvn org.pitest:pitest-maven:mutationCoverage'
          }
          post {
            always {
              pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
            }
          }
        }
      stage('SonarQube - SAAT') {
          steps {
            withSonarQubeEnv('Sonar') {
      	      sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-app -Dsonar.projectName='numeric-app'"
		        }
		      timeout(time: 2, unit: 'MINUTES'){
			      script{
				      waitForQualityGate abortPipeline: true
			      }
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
