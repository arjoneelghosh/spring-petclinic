pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "spring-petclinic"
    }
    stages {
        stage('Build with Maven') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
                sh 'minikube image load $DOCKER_IMAGE'
            }
        }

        stage('Deploy to Kubernetes (Blue & Green)') {
            steps {
                sh 'minikube kubectl -- apply -f blue-deployment.yaml'
                sh 'minikube kubectl -- apply -f green-deployment.yaml'
                sh 'minikube kubectl -- apply -f service.yaml'
            }
        }

        stage('Switch Traffic to Green') {
            steps {
                sh 'minikube kubectl -- patch svc spring-petclinic-service -p \'{"spec":{"selector":{"version":"green"}}}\''
            }
        }
    }
}
