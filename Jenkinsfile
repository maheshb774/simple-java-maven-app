pipeline {
    agent any

    environment {
        IMAGE_NAME = "mlearn774/simple-java-app"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                      docker push $IMAGE_NAME:$BUILD_NUMBER
                    '''
                }
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
}


