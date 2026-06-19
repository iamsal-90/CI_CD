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
                echo '🔍 Analyzing code with SonarQube...'
                // این دستور محیط رو متصل می‌کنه به سروری که در جنکینز ست کردیم
                withSonarQubeEnv('SonarQube-Server') {
                    // اجرای اسکنر سونار از طریق ماون
                    sh 'mvn sonar:sonar -Dsonar.projectKey=simple-java-app'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo '🚦 Checking SonarQube Quality Gate...'
                // این دستور منتظر می‌مونه تا سونار نتیجه رو برگردونه (پاس یا فیل)
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            echo '🧹 Cleaning up workspace...'
        }
        success {
            echo '🎉 Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed! Check the logs.'
        }
    }
}
