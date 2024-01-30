pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('jenkins-aws-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret')
        AWS_S3_BUCKET = 'bucket-demo-ebs-1'
        ARTIFACT_NAME = 'demo-ebs.jar'
        AWS_EB_APP_NAME = 'demo-ebs-app'
        AWS_EB_ENVIRONMENT = 'demo-ebs-app-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
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
                success {
                    sh 'aws configure set region eu-west-3'
                    echo '--====-- Deploying App --===--'
                    sh 'aws s3 cp ./target/*.jar s3://$AWS_S3_BUCKET/$ARTIFACT_NAME'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "--====-- Deploying App --====--"
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            }
        }
    }
}
