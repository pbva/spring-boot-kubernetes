pipeline {
    agent any

    tools {
        maven 'Maven'
    }
    stages {
      stage ('Inicio') {
            steps {
              sh '''
                   echo "PATH = ${PATH}"
                   echo "M2_HOME = ${M2_HOME}"
               '''
            }
        }
        stage ('Compilacion') {
            steps {
                 sh 'mvn clean compile -e'
            }
        }
        stage ('El-Test') {
            steps {
                 sh 'mvn clean test -e'
            }
        }

        stage('SonarQube') {
           steps{
                script {
                    def scannerHome = tool 'SonarQube Scanner';//def scannerHome = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withSonarQubeEnv('Sonar Server') {
                      sh 'mvn sonar:sonar -Dsonar.projectKey=Ms-Maven -Dsonar.host.url=http://172.18.0.3:9000 -Dsonar.login=f02ea950a916c2196fb81575fbf270b425148f15'
                      //sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Maven -Dsonar.host.url=http://172.18.0.3:9000 -Dsonar.login=3669ed23eaa7500048d3d0a87a43669d3db349af -Dsonar.dependencyCheck.jsonReportPath=target/dependency-check-report.json -Dsonar.dependencyCheck.xmlReportPath=target/dependency-check-report.xml -Dsonar.dependencyCheck.htmlReportPath=target/dependency-check-report.html"

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
