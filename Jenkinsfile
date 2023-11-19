pipeline {
    agent any

    environment {
            EMAILS = "thiagofdso.ufpa@gmail.com"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your source code repository (e.g., Git)
                checkout scm
            }
        }

        stage('Test') {
            steps {
                // Run tests using Maven
                script {
                    def mvnHome = tool 'Maven-3.6.3'
                    def mvnCmd = "${mvnHome}/bin/mvn"
                    sh "${mvnCmd} test"
                }
            }
        }

	    stage('Parallel Build') {
           parallel {
                stage('Development') {
                    steps {
                        script {
                            echo "Building for Development environment"
                            def mvnHome = tool 'Maven-3.6.3'
                            def mvnCmd = "${mvnHome}/bin/mvn"
                 		     sh "${mvnCmd} clean package -P development -DskipTests=true"
                        }
                    }
                }

                stage('Production') {
                    steps {
                        script {
                            echo "Building for Production environment"
                            def mvnHome = tool 'Maven-3.6.3'
                            def mvnCmd = "${mvnHome}/bin/mvn"
                 		     sh "${mvnCmd} clean package -P production -DskipTests=true"
                        }
                    }
                }
             }
        }
    }

    post {  
        success {
                echo "Sending success mail"
                emailext body: '${TEMPLATE, file="managed:SuccessMail-Body"}', subject: '${TEMPLATE, file="managed:SuccessMail-Title"}', to: "${EMAILS}"
        }
            
        unsuccessful {
                echo "Sending failed mail"
                emailext attachLog: true, body: '${TEMPLATE, file="managed:FailedMail-Body"}', subject: '${TEMPLATE, file="managed:FailedMail-Title"}', to: "${EMAILS} "
        }
    }
}
