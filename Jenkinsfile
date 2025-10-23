pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "tapashranjannandi/node-app-blogify:latest"
        REGISTRY_CREDENTIALS = credentials('dockerhub-credentials') // DockerHub creds
        GITHUB_REPO = 'https://github.com/TAPASHRANJANNANDI/blogify-app.git'     // Replace if different
        BRANCH = 'master'                                              // Or 'master'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "Cloning repository from GitHub..."
                git branch: "${BRANCH}", url: "${GITHUB_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t node-app:latest .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                echo "Tagging Docker image..."
                sh 'docker tag node-app:latest $DOCKER_IMAGE'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying application to Kubernetes cluster..."
                sh '''
                    kubectl apply -f kubernetes/application/deployment.yaml
                    kubectl apply -f kubernetes/application/service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Blogify deployed successfully to Kubernetes!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs for details."
        }
    }
}

