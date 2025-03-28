pipeline {
    agent any

    environment {
        APP_PORT = '8090'
        JAR_FILE = 'target\\demo-application-0.0.1-SNAPSHOT.jar' // Update with your exact JAR name
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

        stage('Build & Run') {
            steps {
                bat '''
                    mvn clean package
                    netstat -ano | findstr ":8090" && taskkill /F /PID $(netstat -ano | findstr ":8090" | awk "{print \$5}") || echo No app running
                    java -jar target\\demo-application-0.0.1-SNAPSHOT.jar
                '''
            }
        }
    }
}