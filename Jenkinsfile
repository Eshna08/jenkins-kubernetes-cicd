pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Eshna08/jenkins-kubernetes-cicd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-website:v2 .'
            }
        }

        stage('Load Image into Kubernetes') {
            steps {
                sh '''
                    docker save devops-website:v2 -o website.tar
                    docker exec -i minikube ctr -n k8s.io images import - < website.tar
                '''
            }
        }

        stage('Deploy on Kubernetes') {
            steps {
                sh '''
                    /snap/bin/kubectl --kubeconfig=/var/lib/jenkins/.kube/config \
                    set image deployment/devops-website \
                    devops-website=devops-website:v2

                    /snap/bin/kubectl --kubeconfig=/var/lib/jenkins/.kube/config \
                    rollout status deployment/devops-website
                '''
            }
        }
    }
}
