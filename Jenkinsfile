pipeline {

    agent any

    environment {
        DOCKER = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe'
        KUBECTL = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe'
    }

    stages {

        stage('Clone Code') {
            steps {
                echo 'Cloning Student Feedback application'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" build -t balaji00002/student-feedback:v1 .'
            }
        }

        stage('Test Application') {
            steps {
                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" images balaji00002/student-feedback:v1'
            }
        }

        stage('Push Image') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%'

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" push balaji00002/student-feedback:v1'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {

                withCredentials([
                    file(
                        credentialsId: 'kubeconfig',
                        variable: 'KUBECONFIG'
                    )
                ]) {

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" apply -f deployment.yaml'
                }
            }
        }

        stage('Verify Kubernetes') {
            steps {

                withCredentials([
                    file(
                        credentialsId: 'kubeconfig',
                        variable: 'KUBECONFIG'
                    )
                ]) {

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get deployment'

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get pods'

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get service'
                }
            }
        }
    }

    post {

        success {
            echo 'Student Feedback CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}