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

        stage('Deploy') {
            steps {
                script {
                    // 1. Safely find JAR file
                    def jarFile = findFiles(glob: 'target/*.jar')[0]?.path
                    if (!jarFile) {
                        error 'No built JAR file found in target directory'
                    }
                    jarFile = jarFile.replace('/', '\\')

                    // 2. Safely kill existing app (port-based)
                    bat """
                        @echo off
                        setlocal enabledelayedexpansion
                        for /f "tokens=5" %%A in (
                            'netstat -ano ^| findstr ":8090"'
                        ) do (
                            set pid=%%A
                            taskkill /PID !pid! /F || echo Process !pid! not found
                        )
                    """

                    // 3. Start application with error handling
                    bat """
                        @echo off
                        set START_CMD="java -jar \"${jarFile}\""
                        echo Starting application: %START_CMD%
                        start "SpringBootApp" /B cmd /c %START_CMD%
                        timeout /t 5
                        tasklist | find "java.exe" || echo Failed to start application
                    """
                }
            }
        }

        post {
            always {
                echo 'Deployment phase completed'
                // Verify application is running
                bat 'netstat -ano | findstr ":8090" || echo Port 8090 not in use'
            }
        }
    }
}