pipeline 
{
    agent {       
        label 'DEV' 
    }
	    parameters {
        booleanParam(name: "RUN_UNITTETS", defaultValue: true, description: "Select to run UnitTests")
        string(name: "BUILD_VERSION", defaultValue: "1.0", trim: true, description: "Enter the version")
        text(name: "BUILD_DESCRIPTION", defaultValue: "ORCAS CICD Pipeline", description: "Build description")
        //password(name: "TEST_PASSWORD", defaultValue: "SECRET", description: "Sample password parameter")
        choice(name: "TEST_CHOICE", choices: ["production", "staging", "development"], description: "Sample multi-choice parameter")
    }
    environment{
        dotnet = 'C:\\Program Files\\dotnet'
        MSDEPLOY = """C:\\"Program Files (x86)"\\IIS\\"Microsoft Web Deploy V3"\\msdeploy.exe"""    
        }

    stages {
		stage('Gitlab Pending') {
			steps {
				echo 'Notify GitLab'
				updateGitlabCommitStatus name: 'build', state: 'pending'
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}, BUILD_TAG=${env.BUILD_TAG}, NODE_NAME=${env.NODE_NAME}"
			}          	
		}

        stage('Restore Packages') {
            steps {
                echo "Restore Packages"
			}          
        }

        stage('Clean') {
            steps {
                echo "clean artifacts"
			}           
        }

        stage('Build') {
            steps {
                echo "Build artifacts"
			}         
        }

        stage('DB-Migration') {
            steps {
                echo "DB-Migration"
            }	
        }    

       stage('Running Unit Tests') {
			when { 
			  allOf {
				expression { params.RUN_UNITTETS == true }
				expression { branch 'feature/Dev' } 
			  }
			}
             steps {
				 echo 'Running Unit Tests'
             }
            post {
                success {
                    echo 'Archiving Artifacts'
                }

            }             
        }

        stage('Publish') {
             steps {
                echo 'Publish artifacts'
             }           
        }  
      
        stage('Deploy-WebApp') {
             steps {
				echo 'Deploy-WebApp'
             }          
        }

        stage('Deploy-Api') {
             steps {
                echo 'Deploy-Api'
             }          
        }

        stage('Restart Application Pools') {
             steps {
				echo 'Restart Application Pools'                 
             }            
        }     



    }
    post {
        success {
            updateGitlabCommitStatus name: 'test', state: 'success'
           /* mattermostSend (
                color: "good", 
                message: "Build SUCCESS: <${env.BUILD_URL}|${env.JOB_NAME} #${env.BUILD_NUMBER}>"
            )*/                
        }
        failure {
            updateGitlabCommitStatus name: 'test', state: 'failed'
            /*mattermostSend (
                color: "danger", 
                message: "Build FAILED: <${env.BUILD_URL}|${env.JOB_NAME} #${env.BUILD_NUMBER}>"
            )*/
            script {
                currentBuild.displayName = "#" + currentBuild.number + " " + currentBuild.currentResult + " " + "[Gitlab - Success]"
            }
        }
    } 	
}
