pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "spring-petclinic:blue"  // or :green based on which you deploy
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/arjoneelghosh/spring-petclinic.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'minikube kubectl -- apply -f blue-deployment.yaml'
                sh 'minikube kubectl -- apply -f service.yaml'
            }
        }

        stage('Switch Traffic') {
            steps {
                sh '''minikube kubectl -- patch svc spring-petclinic-service \
                    -p '{"spec":{"selector":{"version":"blue"}}}' '''
            }
        }
    }
}
