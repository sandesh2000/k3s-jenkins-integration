pipeline{

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub')
	}

	stages {
		stage('Get Approval for the docker images and k3s resource launch') {
			options {
				      timeout(time: 180, unit: 'MINUTES')
			}
			steps {
				input "Kindly provide the approval for k3s-php-testing"
			}
		}
		stage('Build') {

			steps {
				sh 'docker build -t sandesh2000/k3s-php-jenkins:v10 .'
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push sandesh2000/k3s-php-jenkins:v10'
			}
		}
	}

	post {
		always {
			sh 'docker logout'
		}
	}

}