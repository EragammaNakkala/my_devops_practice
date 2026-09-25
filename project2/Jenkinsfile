pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/EragammaNakkala/Trend.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t trend-app:latest .'
            }
        }

        stage('DockerHub Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_TOKEN'
                    )
                ]) {
                    sh '''
                        echo "$DOCKERHUB_TOKEN" | docker login -u "$DOCKERHUB_USER" --password-stdin
                        docker tag trend-app:latest "$DOCKERHUB_USER/trend-app:latest"
                        docker push "$DOCKERHUB_USER/trend-app:latest"
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                    export KUBECONFIG="/var/lib/jenkins/.kube/config"

                    /usr/local/bin/kubectl apply -f k8s/deployment.yaml
                    /usr/local/bin/kubectl apply -f k8s/service.yaml
                    /usr/local/bin/kubectl rollout status deployment/trend-app
                '''
            }
        }
    }
}