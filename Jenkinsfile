def INSTANCE_IP = "0.0.0.0"
def dockercompose = '''sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose'''

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
                
				echo "${INSTANCE_IP} before setting IP"

                dir('terraform') {
                    sh 'terraform init'
                    script {
                        INSTANCE_IP = sh (script: 'terraform output --raw instance_ip_addr', returnStdout: true).trim()
                    }
                    echo "${INSTANCE_IP} inside dir"
                }
                // validate IP is callable outside subdirectory
                echo "${INSTANCE_IP} outside terraform dir"

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