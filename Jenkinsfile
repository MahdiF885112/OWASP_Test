pipeline {
    agent any

    stages {
        stage('Maven Install') {
            steps {
                script {
                    def mvnHome = tool 'Maven'
                    if (mvnHome == null) {
                        echo 'Maven is not installed. Installing Maven...'
                        def mavenUrl = 'https://downloads.apache.org/maven/maven-3/3.8.8/binaries/apache-maven-3.8.8-bin.tar.gz'
                        sh """
                            curl -fsSL ${mavenUrl} -o apache-maven.tar.gz
                            tar -xzf apache-maven.tar.gz
                            rm apache-maven.tar.gz
                            """
                        withEnv(["PATH+MAVEN=${env.WORKSPACE}/apache-maven-3.8.8/bin"]) {
                            mvnHome = tool 'Maven'
                        }
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
