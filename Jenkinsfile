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
                    def scannerHome = tool 'SonarQube Scanner';
                    withSonarQubeEnv('SonarQube') {
                      sh 'mvn sonar:sonar -Dsonar.projectKey=CursoFinal -Dsonar.host.url=http://172.18.0.3:9000 -Dsonar.login=64d9545c354321893b15ea4bffe82d678062b23e -Dsonar.dependencyCheck.jsonReportPath=target/dependency-check-report.json -Dsonar.dependencyCheck.xmlReportPath=target/dependency-check-report.xml -Dsonar.dependencyCheck.htmlReportPath=target/dependency-check-report.html'
                    }
                }
           }
        }

        stage ('SCA') {
            steps {
                sh 'mvn org.owasp:dependency-check-maven:check'
                dependencyCheckPublisher failedNewCritical: 5, failedTotalCritical: 10, pattern: 'target/dad.xml', unstableNewCritical: 3, unstableTotalCritical: 5
            }
        }
        
              stage('ZAP'){
        			steps {
                 sh 'mvn clean test -e'
                }
        		}
        
        
         stage('Scan Docker'){
                    			steps {
                 sh 'mvn clean test -e'
                }
                    		}	
        
        
    }
    
        post { 
        always {
            script {
                def COLOR_MAP = [
                    'SUCCESS': 'good', 
                    'FAILURE': 'danger',
                ]
                
            }
            publishHTML([
        				    allowMissing: false,
        				    alwaysLinkToLastBuild: false,
        				    keepAll: false,
        				    reportDir: '/var/jenkins_home/tools',
        				    reportFiles: 'zap_baseline_report2.html',
        				    reportName: 'HTML Report',
        				    reportTitles: ''])
            
            /*slackSend channel: 'notificacion-jenkins',
                color: 'danger',
                message: "Se ha terminado una ejecucion del pipeline."*/
        }

    }
}
