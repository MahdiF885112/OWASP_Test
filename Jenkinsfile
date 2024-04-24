pipeline {
    agent any

    stages {
        stage('Maven Install') {
            steps {
                script {
                    if (!fileExists('mvnw')) {
                        echo 'Maven wrapper not found. Downloading...'
                        sh 'curl -o mvnw https://repo.maven.apache.org/maven2/io/takari/maven-wrapper/0.6.1/maven-wrapper-0.6.1.jar'
                        sh 'chmod +x mvnw'
                    } else {
                        echo 'Maven wrapper found.'
                    }
                }
            }
        }

        stage('Build & Unit test') {
            steps {
                script {
                    if (!fileExists('.mvn/wrapper/maven-wrapper.properties')) {
                        echo 'Maven wrapper properties not found. Initializing...'
                        sh './mvnw -N io.takari:maven:wrapper'
                    }
                    sh './mvnw clean package'
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }

        stage('Security SAST: OWASP Dependency Check') {
            steps {
                sh './mvnw verify'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying MyWebAppSecurity to Tomcat 11"
                sh "copy target\\MyWebAppSecurity.war \"C:\\Program Files\\Apache Software Foundation\\Tomcat 11\\webapps\\MyWebAppSecurity.war\""
            }
        }

        stage('Scan ZAP (Active Scan DAST)') {
            steps { 
                // Ensure Docker is installed and running
                sh 'docker --version'
                // Run ZAP scan
                sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://localhost:9090/MyWebAppSecurity/ || exit 0'
            }
        }
    }
}

def fileExists(filePath) {
    def file = new File(filePath)
    return file.exists()
}
