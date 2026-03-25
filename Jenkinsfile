pipeline {
    agent any

    stages {

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t my-k8s-app:${BUILD_NUMBER} .
                docker tag my-k8s-app:${BUILD_NUMBER} sohaa288/my-k8s-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push sohaa288/my-k8s-app:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                # Stop old container if exists
                docker stop my-app || true
                docker rm my-app || true

                # Run new container
                docker run -d -p 3000:3000 --name my-app sohaa288/my-k8s-app:${BUILD_NUMBER}
                '''
            }
        }
    }
}
