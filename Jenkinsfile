pipeline {
    agent any
    stages {
        stage('Jenkins Discovery') {
            steps {
                echo 'Jenkins Discovery'
            }
        }
        stage('Build') { 
            steps {
                sh 'mvn clean package'
            }
        }      
    }
}