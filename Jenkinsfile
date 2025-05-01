pipeline {
  agent any
  environment {
    DOCKER_BUILDKIT = 1
  }

  stages {
    stage('Build with Maven') {
      steps {
        sh './mvnw clean package -DskipTests'
      }
    }

    stage('Build Docker Image in Minikube') {
      steps {
        sh '''
          eval $(minikube -p minikube docker-env)
          docker build -t spring-petclinic .
        '''
      }
    }

    stage('Deploy to Kubernetes (Blue & Green)') {
      steps {
        sh '''
          kubectl apply -f blue-deployment.yaml
          kubectl apply -f green-deployment.yaml
          kubectl apply -f service.yaml
        '''
      }
    }

    stage('Switch Traffic to Green') {
      steps {
        sh '''
          kubectl patch svc spring-petclinic-service -p '{"spec":{"selector":{"version":"green"}}}'
        '''
      }
    }
  }
}
