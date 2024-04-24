#!groovy
pipeline {
    agent none
   stages {     
    stage('Maven Install') {
      agent {         
       docker {          
         image 'maven:3.8.8'         
     }       
  }       
  steps {
       sh 'mvn clean install'
       }
     }
        
        stage('Build & Unit test') {
            steps {
                sh 'mvn clean package'
                junit '**/target/surefire-reports/TEST-*.xml'
            }
        }

        stage('Security SAST: OWASP Dependency Check') {
            steps {
                sh "mvn verify"
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying MyWebAppSecurity to Tomcat 11"
                sh "sudo cp $WORKSPACE/target/MyWebAppSecurity.war /usr/share/tomcat/webapps/MyWebAppSecurity.war"
            }
        }

        stage('Scan ZAP (Active Scan DAST)') {
            steps { 
                sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://localhost:9090/MyWebAppSecurity/ || exit 0'
            }
        }
    }
}

