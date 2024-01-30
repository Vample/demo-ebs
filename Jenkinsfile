pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('jenkins-aws-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret')
        AWS_S3_BUCKET = 'bucket-demo-ebs-1'
        ARTIFACT_NAME = 'demo-ebs.jar'
    }

    stages {
        
        stage('Build') {
            steps {
                echo "--====-- Building App --====--"
                sh 'mvn clean package -DskipTests=true'
                archive 'target/*.jar'
            }
        }
        stage('Test') {
            steps {
                echo "--====-- Testing App --====--"
                sh 'mvn test'
            }
            post {
            always {
              junit 'target/surefire-reports/*.xml'
              jacoco execPattern: 'target/jacoco.exec'
            }
          }
        }
        stage('Deploy') {
            steps {
                echo "--====-- Deploying App --====--"
                sh 'aws --version'
                sh 'aws configure set region eu-west-3'
                sh 'aws s3 ls'
            }
        }
    }
}
