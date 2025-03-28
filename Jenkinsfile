pipeline {
    agent any

    environment {
        APP_PORT = '8090'
    }

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
                bat 'dir /b target\\*.jar'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def jarFile = findFiles(glob: 'target/*.jar')[0]?.path
                    if (!jarFile) error 'No JAR file found'
                    jarFile = jarFile.replace('/', '\\')

                    // Kill existing
                    bat """
                        for /f "tokens=5" %%A in (
                            'netstat -ano ^| findstr ":${env.APP_PORT}"'
                        ) do taskkill /PID %%A /F
                    """

                    // Start new instance
                    bat """
                        start "SpringBootApp" /B cmd /c "java -jar \\\"${jarFile}\\\""
                        timeout /t 10
                        tasklist | find "java.exe" || exit 1
                    """
                }
            }
        }
    }

    post {
        always {
            bat "netstat -ano | findstr \":${env.APP_PORT}\" || echo App not running"
        }
    }
}