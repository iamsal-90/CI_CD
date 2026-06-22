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
                echo '📦 Uploading Artifact to Nexus Repository...'
                // این تگ اطلاعات ورود رو امن از جنکینز می‌خونه و به متغیر تبدیل می‌کنه
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    
                    // برای اینکه در نکسوس ریلیز آپلود کنیم، پسوند SNAPSHOT رو موقتاً برمی‌داریم تا نسخه ریلیز بشه
                    // اجرای دستور پکیج و دیپلوی ماون با تزریق اطلاعات ورود
                    sh 'mvn clean deploy -DskipTests -DaltDeploymentRepository=nexus-releases::default::http://nexus:8081/repository/maven-releases/ -Dusername=${NEXUS_USER} -Dpassword=${NEXUS_PASS}'
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
