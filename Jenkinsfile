pipeline {
    agent any

    tools {
        maven 'M3'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ericmuchenah/demo-application.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
                bat 'dir target\\'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Deploy & Run') {
            steps {
                script {
                    // Kill existing
                    bat 'taskkill /F /IM java.exe || exit 0'

                    // Find and run JAR
                    def jars = findFiles(glob: 'target/*.jar')
                    if (jars) {
                        bat "start /B java -jar ${jars[0].path}"
                        echo "Application started! Access: http://localhost:8090/hello"
                    } else {
                        error "No JAR file found in target directory!"
                    }
                }
            }
        }
    }
}