pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/EmnaDorai/emnaDorai.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
    }

    post {
        failure {
            echo "Erreur dans la pipeline"
        }
    }
}
