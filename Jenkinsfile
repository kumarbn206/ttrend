pipeline {
    agent {
        node {
            label 'maven-agent'
        }
    }

environment {
    PATH = "/opt/apache-maven-3.9.2/bin:$PATH"
}
    stages {

        stage('build the code'){
            steps{
                echo '----------------------------------------------Build Started--------------------------'
                sh 'mvn clean deploy -Dmaven-test-skip=true'
                echo '----------------------------------------------Build Completed--------------------------'
            }
        }

        stage('Unit Test') {
            steps{
                echo '----------------------------------------------Unit Test Started--------------------------'
                sh 'mvn surefire-report:report'
                echo '----------------------------------------------Unit Test Completed--------------------------'
            }
        }



         stage("SonarQube Analysis"){
        environment {
            scannerHome = tool 'SonarScanner'
        }
        steps
        {
            echo '----------------------------------------------SonarQube Started--------------------------'
           withSonarQubeEnv('Sonarqube-server'){
            sh '${scannerHome}/bin/sonar-scanner'
            echo '----------------------------------------------Sonarqube Analysis Completed--------------------------'
           }
        }
    }


     stage("Quality Gate"){
        steps{
             script{
                    timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
                    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                    if (qg.status != 'OK') {
                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                    }
             }
        }
                 
    }

    
    } 

   
}
