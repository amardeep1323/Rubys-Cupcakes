pipeline {
    agent any

    environment {
        IMAGE_NAME = "amardeep1323/rubys-cupcakes"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/amardeep1323/Rubys-Cupcakes.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

      stage('Deploy to Kubernetes') {
    steps {
        sh '''
        kubectl apply -f K8s/

        kubectl rollout restart deployment/rubys-cupcakes

        kubectl rollout status deployment/rubys-cupcakes --timeout=120s

        # Stop any existing port-forward process
        pkill -f "kubectl port-forward" || true

        # Start port-forward in the background
        nohup kubectl port-forward --address 0.0.0.0 svc/rubys-cupcakes-service 8090:80 > port-forward.log 2>&1 &

        sleep 5

        # Verify it started
        cat port-forward.log || true
        '''
    }
}  }
}
