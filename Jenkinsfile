pipeline {
    agent any

environment {
   PATH = "/usr/share/man/man1/mvn.1.gz:$PATH"

}
    stages {
        stage('Git') {
            steps {
               git branch: 'amir', url: 'https://github.com/devCyberops/SpringDataJPA-CrudRepo.git'
            }
        }
            stage('MVN Clean') {
            steps {
                sh 'mvn clean'
                  }
        }
        stage('MVN Compile'){
            steps {
                sh 'mvn compile'
            }
        }
        stage('MVN Build'){
            steps {
                sh 'mvn install'
            }
        }	    
	    stage("Test JUnit - Mockito"){
                steps {
                            sh 'mvn test'
                }
          }
	    
        stage ('SonarQube SAST') {
		steps {
			 sh 'printenv'
              //  sh  "mvn sonar:sonar -Dsonar.login=ee074ba54a7030fc4acf6117d9b3a5490e12febd"
	
		}
	}
            
	    stage("nexus deploy"){
               steps{
		        sh 'printenv'
                    //   sh 'mvn clean deploy -DskipTests'
                    //   sh'mvn clean deploy -Dmaven.test.skip=true -Dresume=false'
               }
          }
	    
stage('Vulnerability Scan - Docker') {
        steps {
          parallel(
 			"Trivy Scan":{
 	 			sh "bash trivy-docker-image-scan.sh"
 	 		},
 		 "OPA Conftest":{
 				sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-docker-security.rego Dockerfile'
 			}

        	)
        }
      }
	
	    
    stage('Docker Build and Push') {
       steps {
        withDockerRegistry([credentialsId: "Docker-Hub-AmirTrigui", url: ""]) {
           sh 'printenv'
//           sh 'docker build -t likeaboos/ci:latest .'
 // sh 'docker push likeaboos/ci:latest '
         }
       }
     }


stage('Docker Compose') {
      steps {
//	      sh 'printenv'
   //            sh 'docker-compose up --d --force-recreate '
       }
     }

	   
	
	
  
}  
	post {

        always {
            echo 'This will always run'
        }
       
        success {
            mail to: "mohamedamir.trigui@esprit.tn",
                     subject: "Success",
                     body: "Succes on job ${env.JOB_NAME}, Build Number: ${env.BUILD_NUMBER} "        
        }
        
        failure {
                    mail to: "mohamedamir.trigui@esprit.tn",
                     subject: "Failure",
                     body: "Failure on job ${env.JOB_NAME}, Build Number: ${env.BUILD_NUMBER}, Build URL: ${env.BUILD_URL} "     
                }

    }
	
}
