pipeline {
    agent any

    tools {
        maven 'M3'
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Fetching code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🛠️ Compiling the Java application...'
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running Unit Tests...'
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=simple-java-app'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                echo '📦 Deploying artifact to Nexus...'
                sh 'mvn clean deploy -DskipTests'
            }
        }

    } // پایان stages

    post {
        always {
            echo '🧹 Cleaning up workspace...'
            cleanWs()
        }

        success {
            echo '🎉 Pipeline completed successfully!'
        }

        failure {
            echo '❌ Pipeline failed! Check the logs.'
        }
    }

} // پایان pipeline
