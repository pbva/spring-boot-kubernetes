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
                    withSonarQubeEnv('SonarQube') {
                      sh 'mvn sonar:sonar -Dsonar.projectKey=Curso -Dsonar.host.url=http://172.18.0.3:9000 -Dsonar.login=74c4d23429c97b548817bc05287ff31c9fce1714 -Dsonar.dependencyCheck.jsonReportPath=target/dependency-check-report.json -Dsonar.dependencyCheck.xmlReportPath=target/dependency-check-report.xml -Dsonar.dependencyCheck.htmlReportPath=target/dependency-check-report.html'
                      //sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Maven -Dsonar.host.url=http://172.18.0.3:9000 -Dsonar.login=3669ed23eaa7500048d3d0a87a43669d3db349af -Dsonar.dependencyCheck.jsonReportPath=target/dependency-check-report.json -Dsonar.dependencyCheck.xmlReportPath=target/dependency-check-report.xml -Dsonar.dependencyCheck.htmlReportPath=target/dependency-check-report.html"

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
        
               stage('DAST') {
            steps {
                script {
                    try {
                        echo "Inicio de Scanneo Dinamico"
                        sh "docker exec zap zap-cli --verbose quick-scan http://pipeline.ironbox.com.ar:8090 -l Medium" 
                        //sh "docker exec zap zap-cli --verbose alerts --alert-level Medium -f json | jq length"
                        currentBuild.result = 'SUCCESS' 
                    }
                    catch (Exception e) {
                            //echo e.getMessage() 
                            //currentBuild.result = 'FAILURE'
                            println ("Revisar Reporte ZAP. Se encontraron Vulnerabilidades.")

                        }
                    }  
                    echo currentBuild.result 
                    echo "Generacion de Reporte"
                    sh "docker exec zap zap-cli --verbose report -o /var/jenkins_home/workspace/Final_master/owasp-quick-scan-report.html --output-format html"
                    publishHTML target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: '/opt/dast/reports',
                        reportFiles: 'owasp-quick-scan-report.html',
                        reportName: 'Analisis DAST'
                      ]          
            }
        }
        
        
        
        
        
        

    }
}
