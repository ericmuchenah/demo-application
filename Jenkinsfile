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
                    // 1. Find the JAR file
                    def jars = findFiles(glob: 'target/*.jar')
                    if (!jars) {
                        error "No JAR file found in target directory!"
                    }
                    def jarPath = jars[0].path.replace('/', '\\')  // Ensure Windows path format

                    // 2. Kill ONLY the specific Spring Boot app (safer approach)
                    bat """
                        @echo off
                        for /f "tokens=2 delims=," %%A in (
                            'wmic process where "commandline like '%%${jarPath}%%'" get processid^,commandline /format:csv'
                        ) do (
                            taskkill /PID %%A /F
                        )
                    """

                    // 3. Start the new instance
                    bat "start \"SpringBootApp\" /B java -jar \"${jarPath}\""

                    echo "Application started! Access: http://localhost:8090/hello"
                }
            }
        }
    }
}