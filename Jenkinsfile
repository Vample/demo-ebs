pipeline {
    agent any

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
            }
        }
    }
}
