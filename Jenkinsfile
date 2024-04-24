pipeline {
    agent any

    environment {
        MAVEN_HOME = tool 'Maven'
        PATH = "$MAVEN_HOME/bin:${env.PATH}"
    }

    stages {
        stage('Maven Install') {
            steps {
                script {
                    if (env.MAVEN_HOME == null) {
                        echo 'Maven is not installed. Installing Maven...'
                        def mavenUrl = 'https://downloads.apache.org/maven/maven-3/3.8.8/binaries/apache-maven-3.8.8-bin.zip'
                        bat """
                            curl -fsSL -o apache-maven.zip ${mavenUrl}
                            tar -xzf apache-maven.zip -C C:\\apache-maven --strip-components=1
                            del apache-maven.zip
                            """
                        env.MAVEN_HOME = 'C:\\apache-maven'
                        echo "Maven installed at ${env.MAVEN_HOME}"
                    } else {
                        echo "Maven is already installed at ${env.MAVEN_HOME}"
                    }
                }
            }
        }

        stage('Build & Unit test') {
            steps {
                bat 'mvn clean package'
                junit '**/target/surefire-reports/TEST-*.xml'
            }
        }

        stage('Security SAST: OWASP Dependency Check') {
            steps {
                bat 'mvn verify'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying MyWebAppSecurity to Tomcat 11"
                bat "copy target\\MyWebAppSecurity.war C:\\Program Files\\Apache Software Foundation\\Tomcat 11\\webapps\\MyWebAppSecurity.war"
            }
        }

        stage('Scan ZAP (Active Scan DAST)') {
            steps { 
                // Ensure Docker is installed and running
                bat 'docker --version'
                // Run ZAP scan
                bat 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://localhost:9090/MyWebAppSecurity/ || exit 0'
            }
        }
    }
}
