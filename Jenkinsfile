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
                    sh "./gradlew test"
                }
            }
        }


    }

    post {  
        always {
            // Record code coverage using Jacoco
            recordCodeCoverage enabled: true, sourceDirectories: 'src', execPattern: '**/build/jacoco/test.exec', classPattern: '**/classes', sourcePattern: '**/src', maximumStalenessDays: 3        }
        success {
                echo "Sending success mail"
                emailext attachmentsPattern: '**/build/reports/jacoco/test/html/index.html', body: '${TEMPLATE, file="managed:SuccessMail-Body"}', subject: '${TEMPLATE, file="managed:SuccessMail-Title"}', to: "${EMAILS}"
        }
            
        unsuccessful {
                echo "Sending failed mail"
                emailext attachmentsPattern: '**/build/reports/jacoco/test/html/index.html', attachLog: true, body: '${TEMPLATE, file="managed:FailedMail-Body"}', subject: '${TEMPLATE, file="managed:FailedMail-Title"}', to: "${EMAILS} "
        }
    }
}
