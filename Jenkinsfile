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
				sh 'docker build -t sandesh2000/k3s-php-jenkins:$BUILD_NUMBER .'
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push sandesh2000/k3s-php-jenkins:$BUILD_NUMBER'
			}
		}

		stage('Launch Application on k3s cluster using docker image') {

			steps {
				sh 'kubectl apply -f manifest.yaml'
			}
	       }
	}

	post {
		always {
			sh 'docker logout'
		}
	}

}