pipeline {
    agent any

    tools {
        maven 'Maven'
    }
  
    stages {
        stage('initial'){
            steps{
             sh '''
              echo "PATH = ${PATH}"
              echo "M2_HOME = ${M2_HOME}"
              '''
            }
        }
        
        stage('Compile'){
            steps{
                sh 'mvn clean compile -e'
            }
        }
        
        stage('Test'){
            steps{
                sh 'mvn clean test -e'
            }
        }
        
        stage('SCA'){
            steps{
                sh 'mvn org.owasp:dependency-check-maven:check'
                
                archiveArtifacts artifacts: 'target/dependency-check-report.html', followSymlinks: false
            }
        }
        
        stage('Sonarqube'){
            steps{
                script{
                    def scannerHome = tool 'Sonarqube'
                    
                    withSonarQubeEnv('Sonarqube'){
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Lab_DevSecOps -Dsonar.sources=. -Dsonar.java.binaries=target/classes Dsonar.projectBaseDir=${env.WORKSPACE} -Dsonar.exclusions='*//test/*/, *//acceptance-test/*/, */.html' -Dsonar.host.url=http://192.168.190.132:9001 -Dsonar.login=988f780a01019f25714e0549b0d3ecf5b1310e72"
                    }
                }
            }
        }
        
    }
    
           
}
