def registry = 'https://kummi.jfrog.io/'
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
                            steps { 
                                script {
                                   timeout(time: 1, unit: 'HOURS'){   
                                   def qg = waitForQualityGate() 
                                   if (qg.status != 'OK'){
                                   error "Pipeline aborted due to quality gate failure: ${qg.status}"
                                   }
                                   }
                                }
                            }
                        }



     stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog-artifact"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }                   

    
    } 

   
}
