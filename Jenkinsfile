pipeline {
    agent any

   tools {
       maven "Maven"
       nodejs "NodeJS"

    }

    stages {

        stage ('Initial') {
            steps {
              echo '========================================='
              echo '                Inicializando '
              echo '========================================='
              sh '''
                   echo "PATH = ${PATH}"
                   echo "M2_HOME = ${M2_HOME}"
               '''
            }
        }
        stage ('Compile') {
            steps {
                echo '========================================='
                echo '                Compilando '
                echo '========================================='
                 sh 'mvn clean compile -e'
            }
        }
        stage ('Test') {
            steps {
                echo '========================================='
                echo '                Testeando '
                echo '========================================='
                sh 'mvn clean test -e'
            }
        }
        stage ('OWASP Dependency-Check Vulnerabilities') {  
            steps {  
                echo '========================================='
                echo '                Dependecy-Check '
                echo '========================================='
                sh 'mvn dependency-check:check' 
                dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'  
            }  
        } 
        
     
        stage('SonarQube - SAST') {
           steps{
               echo '========================================='
              echo '                SonarQubeLAB '
              echo '========================================='
                script {
                    def scannerHome = tool 'Sonarqube';
                    withSonarQubeEnv('Sonarqube') {
                      sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Lab_DevSecOps -Dsonar.sources=target/ -Dsonar.host.url=http://192.168.190.132:9001 -Dsonar.login=988f780a01019f25714e0549b0d3ecf5b1310e72"                
                    }
                }
           }
        }
    }
}
