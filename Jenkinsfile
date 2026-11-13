pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "kp6969/k8s-demo-app"
    }

    stages {
        stage('1. Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/kritak0808/k8s-jenkins-cicd.git'
            }
        }

        stage('2. Build & Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                        def img = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                        img.push()
                        img.push('latest')
                    }
                }
            }
        }

        stage('3. Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig-id']) {
                    sh 'kubectl --insecure-skip-tls-verify=true apply -f deployment.yaml'
                    sh "kubectl --insecure-skip-tls-verify=true set image deployment/web-app-deployment web-app=${DOCKER_IMAGE}:${BUILD_NUMBER}"
                    sh 'kubectl --insecure-skip-tls-verify=true rollout status deployment/web-app-deployment'
                }
            }
        }
    }
}
