pipeline {
    agent any

    tools {
        // معرفی ابزار Maven که در داکر فایل جنکینز نصب کردیم
        // اگر در جنکینز نام خاصی براش نذاشتی، خودش از مسیر سیستم استفاده میکنه
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
                // اجرای دستور کامپایل ماون
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running Unit Tests...'
                // اجرای تست‌های پروژه
                sh 'mvn test'
            }
        }
    }

    post {
        always {
            echo '🧹 Cleaning up workspace or archiving results...'
            // اینجا بعداً گزارش تست‌ها رو آرشیو می‌کنیم
        }
        success {
            echo '🎉 Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed! Check the logs.'
        }
    }
}
