pipeline {
    agent any
    environment {
        dockerImage = ''
    }
	
    stages {
	stage('Maven Clean Build'){
	    def mavenHome = tool name: 'Maven-3.6.3', type: 'maven'   
            def mavenCMD = '${mavenHome}/bin/mvn '
	    sh '${mavenCMD} clean package'
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
			dockerImage = docker.build("rootex/my-app:${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                    withDockerRegistry(
                        credentialsId: 'DOCKER_CREDENTIALS',
                        url: 'https://index.docker.io/v1/') {
                        dockerImage.push("$env.BUILD_NUMBER")
			dockerImage.push("latest")
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
