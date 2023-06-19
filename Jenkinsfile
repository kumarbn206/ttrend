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

    
    } 

   
}
