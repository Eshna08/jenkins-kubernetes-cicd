
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
                sh 'docker build -t devops-website:build-${BUILD_NUMBER} .'
            }
        }

        stage('Load Image into Kubernetes') {
            steps {
                sh '''
                    docker save devops-website:build-${BUILD_NUMBER} -o website.tar
                    docker exec -i minikube ctr -n k8s.io images import - < website.tar
                    rm -f website.tar
                '''
            }
        }

        stage('Deploy on Kubernetes') {
            steps {
                sh '''
                    /snap/bin/kubectl --kubeconfig=/var/lib/jenkins/.kube/config \
                    set image deployment/devops-website \
                    devops-website=devops-website:build-${BUILD_NUMBER}

                    /snap/bin/kubectl --kubeconfig=/var/lib/jenkins/.kube/config \
                    rollout status deployment/devops-website
                '''
            }
        }
    }
}
