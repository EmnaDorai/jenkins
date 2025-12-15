pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        IMAGE_NAME = "emnadorai/myapp"
        TAG = "latest"
    }

    stages {

        // ====== CI PART ======
        stage('GIT') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/EmnaDorai/jenkins'
            }
        }

        stage('Compile Stage') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        // ====== CD PART ======
        stage('Docker Build') {
            steps {
                // Construit l'image Docker directement pour Minikube
                sh 'eval $(minikube docker-env) && docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Deploy to Kubernetes') {
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
            echo "CI/CD terminé avec succès ! Application déployée sur Minikube."
        }
        failure {
            echo "Le pipeline a échoué. Vérifiez les logs."
        }
    }
}
