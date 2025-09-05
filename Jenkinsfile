pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Ekangaki/super-mario-mimic.git'
            }
        }

        stage('Node.js Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Node.js Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Docker Build & Tag') {
            steps {
                sh "docker build -t ekangaki/super-mario-mimic:${env.BUILD_NUMBER} ."
                sh "docker tag ekangaki/super-mario-mimic:${env.BUILD_NUMBER} ekangaki/super-mario-mimic:latest"
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-credential', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo ${DOCKER_PASS} | docker login --username ${DOCKER_USER} --password-stdin"
                    sh "docker push ekangaki/super-mario-mimic:${env.BUILD_NUMBER}"
                    sh "docker push ekangaki/super-mario-mimic:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubernetes-credential', variable: 'KUBE_CONFIG')]) {
                    sh "chmod 600 \"${KUBE_CONFIG}\""
                    sh "kubectl --kubeconfig=\"${KUBE_CONFIG}\" apply -f deploy/"
                }
            }
        }
    }
}
