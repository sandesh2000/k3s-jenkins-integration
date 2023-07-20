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
		stage('Docker build image') {

			steps {
				sh 'docker build -t sandesh2000/k3s-php-jenkins:$BUILD_NUMBER .'
			}
		}

		stage('Docker login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Docker push image') {

			steps {
				sh 'docker push sandesh2000/k3s-php-jenkins:$BUILD_NUMBER'
			}
		}

		stage('Trigger ManifestUpdate') {

			steps {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
			}	
        }
	}

}