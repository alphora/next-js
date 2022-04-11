pipeline{

	agent any
	
	environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub-jpercival')
	}

	stages {

		stage('Docker Build') {

			steps {
				sh "docker build -t contentgroup/vsm:develop ."
			}
		}

		stage('Docker Push') {
			
			steps {
				withCredentials([usernamePassword(credentialsId: 'dockerhub-jpercival', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
				sh 'docker login -u $dockerHubUser -p $dockerHubPassword'
				sh "docker push contentgroup/vsm:develop"
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