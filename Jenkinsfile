pipeline {
    agent any
    environment {
	my-image = "rootex/my-app"
	registryCredential = 'docker-credential'
        dockerImage = ''
    }
	
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Build image') {
            steps {
                script {
			dockerImage = docker.build("my-image:${env.BUILD_ID}")
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                  withRegistry(
			credentialsId: 'docker-credential',
                        url: 'https://index.docker.io/v1/') {
			dockerImage.push()
		    } 
                }
            }
        }
	stage('Remove Unused docker image') {
	    steps{
		sh "docker rmi $my-image:${env.BUILD_ID}"
	    }
	}
        stage('Deployment') {
            steps {
                sh 'kubectl apply -f deployment.yml';
            }
        }
    }
}
