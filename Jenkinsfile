pipeline {
    agent any
    stages {
        stage('Jenkins Discovery') {
            steps {
                echo 'Jenkins Discovery'
            }
        }
        stage('Build') { 
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Build Image') {
            steps {
                script {
                    discovery = docker.build("alfredjynx/discovery:${env.BUILD_ID}", "-f Dockerfile .")
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        discovery.push("${env.BUILD_ID}")
                        discovery.push("latest")
                    }
                }
            }
        }

        stage('Deploy on k8s') {
            steps {
                withCredentials([ string(credentialsId: 'minikube-credentials', variable: 'api_token') ]) {
                    sh 'kubectl --token $api_token --server https://host.docker.internal:53474  --insecure-skip-tls-verify=true apply -f ./k8s/configmap.yaml '
                    sh 'kubectl --token $api_token --server https://host.docker.internal:53474  --insecure-skip-tls-verify=true apply -f ./k8s/deployment.yaml '
                    sh 'kubectl --token $api_token --server https://host.docker.internal:53474  --insecure-skip-tls-verify=true apply -f ./k8s/service.yaml '
                }
            }
        }   
    }
}