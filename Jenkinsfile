pipeline {
    agent any
    
    environment {
        DOCKER_HUB_REPO = "imransetiadi22/flask-hello-world"
        CONTAINER_NAME = "flask-hello-world"
        DOCKERHUB_CREDENTIALS=credentials('dockerhub-credentials')
    }
    
    stages {
        /* We do not need a stage for checkout here since it is done by default when using "Pipeline script from SCM" option. */
        
        stage('Build images') {
            steps {
                echo 'Building..'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker image build -t $DOCKER_HUB_REPO:latest .'
            }
        }
        stage('Test running program') {
            steps {
                echo 'Testing..'
                sh 'docker stop $CONTAINER_NAME || true'
                sh 'docker rm $CONTAINER_NAME || true'
                sh 'docker run --name $CONTAINER_NAME $DOCKER_HUB_REPO /bin/bash -c "pytest test.py && flake8"'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing image..'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $DOCKER_HUB_REPO:latest'
            }
        }
        stage('Deploy to Cluster Kubernetes') {
            steps {
                echo 'Deploying....'
                sh 'sudo kubectl apply -f deployment.yaml'
                sh 'sudo kubectl apply -f service.yaml'
            }
        }
    }
}
