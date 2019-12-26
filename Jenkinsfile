pipeline {

    environment {
		registry = "rootex/my-app"
		registryCredential = 'docker-credential'
        dockerImage = ''
    }
	agent any
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
                    //dockerImage = docker.build("rootex/my-app")
					docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                  docker.withRegistry( '', registryCredential ) {
					dockerImage.push()
					} 
                }
            }
        }
        stage('Deployment') {
            steps {
                sh 'kubectl apply -f deployment.yml';
            }
        }
    }
}
