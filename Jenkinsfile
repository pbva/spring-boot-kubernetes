pipeline {
    agent any

    tools {
        maven 'Maven'
    }
    stages {
      stage ('Initial') {
            steps {
              sh '''
                   echo "PATH = ${PATH}"
                   echo "M2_HOME = ${M2_HOME}"
               '''
            }
        }
        stage ('Complicacion') {
            steps {
                 sh 'mvn clean compile -e'
            }
        }
        stage ('Test') {
            steps {
                 sh 'mvn clean test -e'
            }
        }

        stage('SonarQube') {
           steps{
                script {
                    def scannerHome = tool name: 'SonarQube Scanner';
                    withSonarQubeEnv('SonarQube') {
                      sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Ms-Maven -Dsonar.sources=target/ -Dsonar.host.url=http://localhost:9000 -Dsonar.login=f75063249f209b4e1faccb9a692547a0c5c9e3b6"
                    }
                }
           }
        }

        stage ('SCA') {
            steps {
                 sh 'mvn org.owasp:dependency-check-maven:check'
                dependencyCheckPublisher failedNewCritical: 5, failedTotalCritical: 10, pattern: 'terget/dad.xml', unstableNewCritical: 3, unstableTotalCritical: 5
            }
        }

    }
}
