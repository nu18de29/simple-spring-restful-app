pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''withMaven(mavenOpts: MAVEN_OPTS, maven: \'M3\', mavenLocalRepo: MAVEN_LOCAL_REPOSITORY, mavenSettingsConfig: MAVEN_SETTINGS) {
    sh "mvn ..."
}'''
        }
      }

      stage('Test') {
        post {
          always {
            junit 'target/surefire-reports/*.xml'
          }

        }
        steps {
          sh 'mvn test'
        }
      }

      stage('Build image') {
        steps {
          script {
            dockerImage = docker.build("phayao/my-app")
          }

        }
      }

      stage('Push image') {
        steps {
          script {
            withDockerRegistry(
              credentialsId: 'docker-credential',
              url: 'https://index.docker.io/v1/') {
                dockerImage.push()
              }
            }

          }
        }

        stage('Deployment') {
          steps {
            sh 'kubectl apply -f deployment.yml'
          }
        }

      }
      environment {
        dockerImage = ''
      }
    }