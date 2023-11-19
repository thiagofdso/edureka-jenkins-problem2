pipeline {
    agent any

    environment {
        EMAILS = "thiagofdso.ufpa@gmail.com"
        JAVA_HOME = tool 'Java-8'
        GRADLE_HOME = tool 'Gradle-8.4'
        PATH = "${GRADLE_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your source code repository (e.g., Git)
                checkout scm
            }
        }

	    stage('Build') {
            steps {
                script {
                    echo "Building"
                    sh "./gradlew clean build"
                }
            }
        }

        stage('Test') {
            steps {
                // Run tests using Gradle
                script {
                    sh "./gradlew test jacocoTestReport"
                }
            }
        }
    }

    post {  
        always {
            // Record code coverage using Cobertura
            echo "Recording coverage"
            recordCoverage enabled: true, tool: jacoco(pattern: '**/build/reports/jacoco/test/jacocoTestReport.xml')
        }
        success {
                echo "Sending success mail"
                emailext attachmentsPattern: '**/build/reports/jacoco/test/jacocoTestReport.xml', body: '${TEMPLATE, file="managed:SuccessMail-Body"}', subject: '${TEMPLATE, file="managed:SuccessMail-Title"}', to: "${EMAILS}"
        }
            
        unsuccessful {
                echo "Sending failed mail"
                emailext attachmentsPattern: '**/build/reports/jacoco/test/jacocoTestReport.xml', attachLog: true, body: '${TEMPLATE, file="managed:FailedMail-Body"}', subject: '${TEMPLATE, file="managed:FailedMail-Title"}', to: "${EMAILS} "
        }
    }
}
