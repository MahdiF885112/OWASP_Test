pipeline {
    agent any

    stages {
        stage('Install Maven') {
            steps {
                script {
                    def mvnHome = tool 'Maven'
                    if (mvnHome == null) {
                        echo 'Maven is not installed. Installing Maven...'
                        def mvnVersion = '3.8.4' // Change this to the version you want to install
                        def mvnUrl = "https://downloads.apache.org/maven/maven-3/${mvnVersion}/binaries/apache-maven-${mvnVersion}-bin.tar.gz"
                        def mvnHome = tool name: 'Maven', type: 'maven', installMethod: 'COMMAND', 
                                          properties: [[$class: 'InstallerLocation', 
                                                        installSource: [
                                                            [$class: 'CommandInstaller', 
                                                             command: "curl -sSL ${mvnUrl} | tar xz"]]]]
                        echo "Maven installed at ${mvnHome}"
                    } else {
                        echo "Maven is already installed at ${mvnHome}"
                    }
                }
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

