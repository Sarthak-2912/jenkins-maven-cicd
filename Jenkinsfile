
pipeline {
    agent any

    tools {
        maven 'Maven-3'
    }

    environment {
        DEPLOY_DIR = 'C:\\jenkins-deployed-app'
        APP_JAR = 'target\\jenkins-maven-cicd-1.0.0.jar'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                echo 'Compiling Java application...'
                bat 'mvn clean compile'
            }
        }

        stage('Unit Testing') {
            steps {
                echo 'Running JUnit tests...'
                bat 'mvn test'
            }
            post {
                always {
                    junit allowEmptyResults: false,
                          testResults: 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package Application') {
            steps {
                echo 'Creating JAR file...'
                bat 'mvn package -DskipTests'

                archiveArtifacts artifacts: 'target/*.jar',
                                 fingerprint: true
            }
        }

        stage('Deploy Automatically') {
            steps {
                echo 'Deploying application on Windows...'

                bat '''
                    if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"
                    copy /Y "%APP_JAR%" "%DEPLOY_DIR%\\app.jar"
                    java -jar "%DEPLOY_DIR%\\app.jar"
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS! All pipeline stages completed.'
        }

        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
