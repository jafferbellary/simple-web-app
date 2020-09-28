pipeline {
    agent any
    tools{
	   maven "Maven"
    } 
     stages {
         stage("Build") {
             steps {
                 echo "Building an application using maven"
				sh 'mvn clean package'
             }
	     post{
			success{
				echo "Artifact is generated successfully"
				echo "Archiving an artifact"
				archiveArtifacts artifacts: '**/*.war', followSymlinks: false
			}
			failure{
		      echo "Failed to generate an artifact"
			}
	     }
         }
         stage("Deploying to staging and perform code analysis"){
			parallel{
				stage("Deploy to staging"){
					steps{
						deploy adapters: [tomcat8(credentialsId: 'e96f8189-33d4-4334-b69f-c0e4e00847eb', path: '', url: 'http://ec2-3-128-204-174.us-east-2.compute.amazonaws.com:8888/')], contextPath: null, onFailure: false, war: '**/*.war'
					}
				post{
					success{
						echo "Application is deployed successfully on Tomcat in staging environment"	
					}
					failure{
						echo "Failed to deploy an application in staging environment"
					}
					always {
            					emailext body: 'A Test Email', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Jenkins Execution Status'
        				}

				}
			}
			
			stage("Perform code analysis"){
				steps{
					sh 'mvn checkstyle:checkstyle'
				}
				post{
					success{
						checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
					}
				}
			}
			}
		 }
			
        }
}
