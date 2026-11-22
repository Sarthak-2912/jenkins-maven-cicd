pipeline {
    agent any

    tools {
        // Must match the Maven name configured in Jenkins -> Tools
        maven 'Maven-3'
    }

    environment {
        DEPLOY_DIR = '/tmp/jenkins-deployed-app'
        APP_JAR = 'target/jenkins-maven-cicd-1.0.0.jar'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out source code from Git...'
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                echo 'Compiling Java source code...'
                sh 'mvn clean compile'
            }
        }

        stage('Unit Testing') {
            steps {
                echo 'Running JUnit tests...'
                sh 'mvn test'
            }
            post {
                always {
                    // Publishes test results to the Jenkins dashboard
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package Application') {
            steps {
                echo 'Packaging application into JAR...'
                // -DskipTests avoids running tests twice since they ran in the previous stage
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Deploy Automatically') {
            steps {
                echo 'Deploying application to target environment...'
                sh '''
                    mkdir -p ${DEPLOY_DIR}
                    cp ${APP_JAR} ${DEPLOY_DIR}/app.jar
                    java -jar ${DEPLOY_DIR}/app.jar
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully! Application is live.'
        }
        failure {
            echo 'Pipeline failed. Check console logs or test reports.'
        }
    }
}