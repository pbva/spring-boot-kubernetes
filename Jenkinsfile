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
        			steps{
        			    //figlet 'Owasp Zap DAST'
        				script{
        				    env.DOCKER = tool 'Docker'
                            //env.DOCKER_EXEC = '${DOCKER}/bin/docker'
        				    env.DOCKER_EXEC = '${DOCKER}/docker/bin'
        				    env.TARGET = 'http://zero.webappsecurity.com'
        				   echo "PATH = ${DOCKER}"
        				    
        				    //sh '${DOCKER_EXEC} rm -f zap2'
        				    sh '${DOCKER_EXEC} pull owasp/zap2docker-stable'
                            sh '${DOCKER_EXEC} run --add-host="localhost:127.0.0.1" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8090:8080 -d owasp/zap2docker-stable zap.sh -daemon -port 8080 -host 0.0.0.0 -config api.disablekey=true'
                            sh '${DOCKER_EXEC} run --add-host="localhost:127.0.0.1" -v /Users/Patricio/Desktop/Resultado:/zap/wrk/:rw --rm -i owasp/zap2docker-stable zap-baseline.py -t "http://zero.webappsecurity.com" -I -r zap_baseline_report2.html -l PASS'
        				}
        			}
        		}
        
        
         stage('Scan Docker'){
                    			steps{
                    			    //figlet 'Scan Docker'
                    		        script{
                    		            env.DOCKER = tool 'Docker'
        				                env.DOCKER_EXEC = "${DOCKER}/bin/docker"
        				             
                                        sh '''
                                            ${DOCKER_EXEC} run --rm -v $(pwd):/root/.cache/ aquasec/trivy python:3.4-alpine
                                        '''
                                       
                                         sh '${DOCKER_EXEC} rmi aquasec/trivy'
    
                    		        }
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
