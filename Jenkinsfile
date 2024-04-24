pipeline {
    agent any

    tools {
         maven "maven" 
    }
    stages {       
      
           

             stage('Build & Unit test'){
                steps {
                      sh 'mvn clean package';
                      junit '**/target/surefire-reports/TEST-*.xml'

                   }
               }

             stage('Security SAST : OWASP Dependency Check'){
                steps {
                
                    sh "mvn verify"
                   }
               }

             stage('Deploy to Tomcat'){
                steps {
                    echo "Deploying MyWebbAppSecurity to Tomcat 11"
                    sh "sudo cp $WORKSPACE/target/MyWebAppSecurity.war /usr/share/tomcat/webapps/mywebapp.war"

                   }
               }

             stage('Scan ZAP(Active Scan DAST)'){
                steps { 
                    sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t  http://13.38.98.152:9090/mywebapp/  || exit 0 '
                     }
               }
        }
}
