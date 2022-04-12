pipeline {
    agent any 
     tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "localmaven"
        git "Default"
    }
    stages {
        stage('SCM Checkout') {
            steps{
                git branch: 'chatapp', credentialsId: 'git-tok', url: 'https://github.com/pavanudugula/tomcat-playbook.git'
            }
        }
        stage('MVN Build') {
          steps{
             sh "mvn -Dmaven.test.failure.ignore=true clean package"
          }
       }
       stage('MVN Testing') {
          steps{
             sh "mvn test"
          }
       }
       stage('test report') {
          steps{
             junit 'target/surefire-reports/*.xml'
          }
          post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
       }
       stage('Get ansible code') {
          steps{
             
                git "https://github.com/pavanudugula/tomcat-playbook.git"
          }
       }
        stage('execute ansible') {
          steps{
             
                sshagent(['tomcat-pass']) {
                 ansiblePlaybook inventory:  'prod.inv',disableHostKeyChecking: true,  playbook: 'tomcat.yml'
              }

          }
       }
    }
}
