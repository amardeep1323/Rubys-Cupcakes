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
        echo "Checking Kubernetes access..."
        kubectl get nodes

        echo "Checking k8s files..."
        ls -la k8s/

        echo "Deploying..."
        kubectl apply -f k8s/

        echo "Restarting deployment..."
        kubectl rollout restart deployment/rubys-cupcakes

        echo "Checking rollout..."
        kubectl rollout status deployment/rubys-cupcakes --timeout=120s
        '''
    }
}    }
}
