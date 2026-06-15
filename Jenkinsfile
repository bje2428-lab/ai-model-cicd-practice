pipeline {
    agent any

    environment {
        IMAGE_NAME = "bje2428/human3_05_docker:latest"
        CONTAINER_NAME = "ai-model-api"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/bje2428-lab/ai-model-cicd-practice.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Docker Hub Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker pull $IMAGE_NAME
                docker run -d -p 8000:8000 --name $CONTAINER_NAME $IMAGE_NAME
                '''
            }
        }

        stage('Test API') {
            steps {
                sh '''
                sleep 5
                curl http://host.docker.internal:8000/
                curl "http://host.docker.internal:8000/predict?value=1"
                '''
            }
        }
    }
}
