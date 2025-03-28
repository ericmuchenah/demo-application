pipeline {
    agent any

    environment {
        APP_PORT = '8090'
        JAR_FILE = 'target\\demo-0.0.1-SNAPSHOT.jar' // Update with your exact JAR name
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
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // 1. Verify JAR exists
                    if (!fileExists(env.JAR_FILE)) {
                        error "JAR file not found at ${env.JAR_FILE}"
                    }

                    // 2. Kill existing process (if any)
                    bat """
                        @echo off
                        for /f "tokens=5" %%A in (
                            'netstat -ano ^| findstr ":${env.APP_PORT}"'
                        ) do (
                            echo Killing process PID %%A
                            taskkill /PID %%A /F
                        )
                    """

                    // 3. Start application (simple version)
                    bat """
                        @echo off
                        echo Starting application from %CD%\\${env.JAR_FILE}
                        java -jar "${env.JAR_FILE}" > springboot.log 2>&1 &
                        echo Application started on port ${env.APP_PORT}
                    """
                }
            }
        }
    }
}