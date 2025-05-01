pipeline {
    agent any

    environment {
        IMAGE_NAME = 'spring-petclinic:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/arjoneelghosh/spring-petclinic.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image in Minikube') {
            steps {
                sh '''
                    eval $(minikube docker-env)
                    docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Deploy to Kubernetes (Blue & Green)') {
            steps {
                sh '''
                    eval $(minikube docker-env)
                    minikube kubectl -- apply -f blue-deployment.yaml
                    minikube kubectl -- apply -f green-deployment.yaml
                    minikube kubectl -- apply -f service.yaml
                '''
            }
        }

        stage('Switch Traffic to Green') {
            steps {
                sh '''
                    minikube kubectl -- patch svc spring-petclinic-service -p '{"spec":{"selector":{"app":"spring-petclinic","version":"green"}}}'
                '''
            }
        }
    }
}
