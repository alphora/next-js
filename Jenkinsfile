pipeline{

	agent any

	env.DOCKER_LABEL = 'develop'
	
	environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub-jpercival')
	}

	stages {

		stage('Docker Build') {

			steps {
				sh "docker build -t contentgroup/vsm:${env.DOCKER_LABEL} ."
			}
		}

		stage('Docker Push') {
			
			steps {
				withCredentials([usernamePassword(credentialsId: 'dockerhub-jpercival', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
				sh 'docker login -u $dockerHubUser -p $dockerHubPassword'
				sh "docker push contentgroup/mypain:${env.DOCKER_LABEL}"
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