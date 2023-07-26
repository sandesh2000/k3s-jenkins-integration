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
				sh 'docker build -f dockerfiles/Dockerfile.product -t sandesh2000/productapp:$BUILD_NUMBER .'
				sh 'docker build -f dockerfiles/Dockerfile.image -t sandesh2000/imageapp:$BUILD_NUMBER .'
			}
		}

		stage('Docker login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Docker push image') {

			steps {
				sh 'docker push sandesh2000/productapp:$BUILD_NUMBER'
				sh 'docker push sandesh2000/imageapp:$BUILD_NUMBER'
			}
		}
		stage('Update Manifest file k3s') {
            steps {
						catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    	withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
						sh "cat manifest.yaml"
                        sh "sed -i 's+sandesh2000/productapp.*+sandesh2000/productapp:${env.BUILD_NUMBER}+g' manifest.yaml"
						sh "sed -i 's+sandesh2000/imageapp.*+sandesh2000/imageapp:${env.BUILD_NUMBER}+g' manifest.yaml"
                        sh "cat manifest.yaml"
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/k3s-jenkins-integration.git HEAD:master"
    
  					}
				}
			}
		}
	}

}