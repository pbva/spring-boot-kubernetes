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
                            script{
                                //Variables Docker
                                env.DOCKER = tool "Docker"
                                env.DOCKER_EXEC = "${DOCKER}/bin/docker"
                                echo "${DOCKER_EXEC}"
                                //elimina imagne docker zap2
                                //sh "${DOCKER_EXEC} rm -f zap2"
                                // descraga la version estable de zap
                                sh "${DOCKER_EXEC} pull owasp/zap2docker-stable"
                                //Levantar el zap en modo escuche
                                    sh '${DOCKER_EXEC} run --add-host="localhost:127.0.0.1" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8090:8090 -d owasp/zap2docker-stable zap.sh -daemon -port 8080 -host 0.0.0.0 -config api.disablekey=true'
                                    // ahora ejejcutamos el scan
                                    sh '${DOCKER_EXEC} run --add-host="localhost:127.0.0.1" -v /home/kali/DevSecOps/zap:/zap/wrk/:rw --rm -i owasp/zap2docker-stable zap-full-scan.py -t "http://zero.webappsecurity.com" -I -r zap_baseline_report2.html -l PASS'
                            }
                         }
                       }
        
        
        
        
        

    }
}
