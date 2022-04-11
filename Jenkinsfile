pipeline{

	agent any
	
	environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub-jpercival')
	}

	stages {

		stage('Docker Build') {

			steps {
				sh "docker build -t contentgroup/valueset-manager:latest ."
			}
		}

		stage('Docker Push') {
			
			steps {
				withCredentials([usernamePassword(credentialsId: 'dockerhub-jpercival', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
				sh 'docker login -u $dockerHubUser -p $dockerHubPassword'
				sh "docker push contentgroup/valueset-manager:latest"
				}
			}
    	}

		stage ('Deploy') {
            when { 
                branch 'main'
             }
            steps{
                
                sshagent(credentials : ['aphl_infrastructure']) {
                    
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@${INSTANCE_IP} date"
                    
                }
            }
        }

		
	}

	post {
		always {
			sh 'docker logout'
		}
	}

}