pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE = "rohansulakhe/react-jenkins-docker-k8s"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RohanSulakhe/Final2.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    // Build Docker image
                    docker.build(DOCKER_IMAGE)
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    // Authenticate and push image to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', 'DOCKER_HUB_CREDENTIALS') {
                        docker.image(DOCKER_IMAGE).push("latest")
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Ensure kubectl is available, or use the following to install it if needed
                    sh """
                    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
                    chmod +x ./kubectl
                    mv ./kubectl /usr/local/bin/kubectl
                    """

                    // Apply Kubernetes manifests with the correct workspace paths
                    sh "kubectl apply -f ${pwd()}/k8s-deployment/k8s-deployment.yaml"
                    sh "kubectl apply -f ${pwd()}/k8s-deployment/k8s-service.yaml"
                }
            }
        }
    }
}
