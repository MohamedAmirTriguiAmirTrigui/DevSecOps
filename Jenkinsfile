/////// ******************************* Code for fectching Failed Stage Name ******************************* ///////
import io.jenkins.blueocean.rest.impl.pipeline.PipelineNodeGraphVisitor
import io.jenkins.blueocean.rest.impl.pipeline.FlowNodeWrapper
import org.jenkinsci.plugins.workflow.support.steps.build.RunWrapper
import org.jenkinsci.plugins.workflow.actions.ErrorAction

// Get information about all stages, including the failure cases
// Returns a list of maps: [[id, failedStageName, result, errors]]
@NonCPS
List<Map> getStageResults( RunWrapper build ) {

    // Get all pipeline nodes that represent stages
    def visitor = new PipelineNodeGraphVisitor( build.rawBuild )
    def stages = visitor.pipelineNodes.findAll{ it.type == FlowNodeWrapper.NodeType.STAGE }

    return stages.collect{ stage ->

        // Get all the errors from the stage
        def errorActions = stage.getPipelineActions( ErrorAction )
        def errors = errorActions?.collect{ it.error }.unique()

        return [
            id: stage.id,
            failedStageName: stage.displayName,
            result: "${stage.status.result}",
            errors: errors
        ]
    }
}

// Get information of all failed stages
@NonCPS
List<Map> getFailedStages( RunWrapper build ) {
    return getStageResults( build ).findAll{ it.result == 'FAILURE' }
}

/////// ******************************* Code for fectching Failed Stage Name ******************************* ///////


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
	      sh 'printenv'
   //            sh 'docker-compose up --d --force-recreate '
       }
     }

	    stage('OWASP ZAP - DAST') {
        steps {
            sh 'bash zap.sh'
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
