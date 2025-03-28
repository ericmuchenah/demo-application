pipeline {
    agent any

    tools {
        maven 'M3'  // Ensure Maven is configured in Jenkins (Manage Jenkins > Tools)
    }

    stages {
        // Stage 1: Clone the repo
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ericmuchenah/demo-application.git'
            }
        }

        // Stage 2: Build the project
        stage('Build') {
            steps {
                sh 'mvn clean package'  // Builds the JAR file
            }
        }

        // Stage 3: Run unit tests
        stage('Test') {
            steps {
                sh 'mvn test'  // Runs tests (already part of `package` but explicit here)
            }
        }

        // Stage 4: Deploy & Run locally
        stage('Deploy & Run') {
            steps {
                sh 'pkill -f "java -jar target/demo-0.0.1-SNAPSHOT.jar" || true'  // Kill existing app
                sh 'nohup java -jar target/demo-0.0.1-SNAPSHOT.jar &'  // Run in background
                echo "Application started! Access: http://localhost:8090/hello"
            }
        }
    }
}