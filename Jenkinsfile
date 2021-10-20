pipeline {
    agent any

    tools {
        maven 'Maven'
    }
  
    stages {
        stage('Inicializando '){
            steps{
             sh '''
              echo "PATH = ${PATH}"
              echo "M2_HOME = ${M2_HOME}"
              '''
            }
        }
        
        stage('Compilando'){
            steps{
                sh 'mvn clean compile -e'
            }
        }
        
        stage('Testeando'){
            steps{
                sh 'mvn clean test -e'
            }
        }
        
        stage('Dependecy-Check'){
            steps{
                sh 'mvn org.owasp:dependency-check-maven:check'
                
                archiveArtifacts artifacts: 'target/dependency-check-report.html', followSymlinks: false
            }
        }
        
        stage('SonarQubeLAB'){
            steps{
                script{
                    def scannerHome = tool 'SonarQube'
                    
                    withSonarQubeEnv('SonarQube'){
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Lab_DevSecOps -Dsonar.sources=. -Dsonar.java.binaries=target/classes -Dsonar.projectBaseDir=${env.WORKSPACE} -Dsonar.exclusions='*//test/*/, *//acceptance-test/*/, */.html' -Dsonar.host.url=http://192.168.190.132:9001 -Dsonar.login=988f780a01019f25714e0549b0d3ecf5b1310e72"
                    }
                }
            }
        }
               stage('DAST'){
            steps{
                figlet 'Owasp Zap DAST'
        		
        		script{
        		   env.DOCKER = tool "Docker"
        		   env.DOCKER_EXEC = "${DOCKER}/bin/docker"
        		   env.TARGET = 'https://demo.testfire.net/'
        	
        		    sh '${DOCKER_EXEC} rm -f zap2'
        		    sh "${DOCKER_EXEC} pull owasp/zap2docker-stable"
                    sh '${DOCKER_EXEC} run --add-host="localhost:192.168.100.4" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8093:8093 -d owasp/zap2docker-stable zap.sh -daemon -port 8093 -host 0.0.0.0 -config api.disablekey=true'
                    sh '${DOCKER_EXEC} run --add-host="localhost:192.168.100.4" -v /Users/asajuro/Documents/BCI/AnalyzeQAS/Jenkins-Practica/USACH/ayudantia/full/jenkins_home/tools:/zap/wrk/:rw --rm -i owasp/zap2docker-stable zap-baseline.py -t "https://demo.testfire.net/" -I -r zap_baseline_report.html -l PASS'	
        		   
        		   publishHTML([
        				    allowMissing: false,
        				    alwaysLinkToLastBuild: false,
        				    keepAll: false,
        				    reportDir: '/var/jenkins_home/tools/',
        				    reportFiles: 'zap_baseline_report.html',
        				    reportName: 'HTML Report',
        				    reportTitles: ''])
        		}
            }
        }
        
           stage('Scan Docker'){
                    			steps{
                    			    figlet 'Scan Docker'
                    		        script{
                    		            env.DOCKER = tool "Docker"
        				                    env.DOCKER_EXEC = "${DOCKER}/bin/docker"

                                        sh '''
                                            ${DOCKER_EXEC} run --rm -v $(pwd):/root/.cache/ aquasec/trivy python:3.4-alpine
                                        '''

                                         sh '${DOCKER_EXEC} rmi aquasec/trivy'
                    		        }
                    			}
            }

            stage('Slack'){
                      steps{
                          figlet 'Slack Message'
                          
                          slackSend channel: 'lab1', message: 'esta es una prueba'
                          
                            slackSend channel: 'notificacion-jenkins',
                            color: 'danger',
                            message: "Se ha terminado una ejecucion del pipeline."
                      }
                  }

    }
    
      
    
           
}
    }
    
           
}
