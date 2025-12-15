pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        IMAGE_NAME = "emnadorai/myapp"
        TAG = "latest"
        DOCKER_HUB_CREDENTIALS = "dockerhub-credentials" // ID dans Jenkins
    }

    stages {

        // ====== CI ======
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/EmnaDorai/jenkins'
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        // ====== Docker Build & Push ======
        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_HUB_CREDENTIALS) {
                        sh "docker build -t $IMAGE_NAME:$TAG ."
                        sh "docker push $IMAGE_NAME:$TAG"
                    }
                }
            }
        }

        // ====== Deploy to Minikube ======
        stage('Deploy MySQL') {
            steps {
                sh '''
                kubectl apply -f k8s/mysql/mysql-secret.yaml
                kubectl apply -f k8s/mysql/mysql-deployment.yaml
                kubectl apply -f k8s/mysql/mysql-service.yaml
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline CI/CD terminé avec succès ! Application déployée sur Minikube et image poussée sur Docker Hub."
        }
        failure {
            echo "Le pipeline a échoué. Vérifie les logs."
        }
    }
}
