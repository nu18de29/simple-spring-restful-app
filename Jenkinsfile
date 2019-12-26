pipeline {
    agent any
    environment {
	registry = "rootex/my-app"
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
			dockerImage = docker.build("registry:${BUILD_NUMBER}")
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
		sh "docker rmi $registry:$BUILD_NUMBER"
	    }
	}
        stage('Deployment') {
            steps {
                sh 'kubectl apply -f deployment.yml';
            }
        }
    }
}
