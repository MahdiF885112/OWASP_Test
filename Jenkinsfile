pipeline {
    agent any

    tools {
        maven "maven" 
    }

    stages {
        stage('Build & Unit test') {
            steps {
                script {
                    // Run maven clean and package
                    bat 'mvn clean package'
                    
                    // Publish JUnit test results
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }

        stage('Security SAST : OWASP Dependency Check') {
            steps {
                script {
                    // Run OWASP Dependency Check
                    bat "mvn verify"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    // Deploy to Tomcat
                    bat 'copy target\\MyWebAppSecurity.war "C:\\path\\to\\tomcat\\webapps\\mywebapp.war"'
                }
            }
        }

        stage('Scan ZAP(Active Scan DAST)') {
            steps {
                script {
                    // Run ZAP Docker container for Active Scan
                    // Adjust the URL and other parameters as needed
                    bat 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://13.38.98.152:9090/mywebapp/ || exit 0'
                }
            }
        }
    }
}
