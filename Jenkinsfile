pipeline {

    agent any

    environment {
        DOCKER = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe'
        KUBECTL = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe'
    }

    stages {

        stage('Clone Code') {
            steps {
                echo 'Cloning Student Feedback application from GitHub'

                checkout scm
            }
        }


        stage('Build Docker Image') {
            steps {

                echo 'Building Student Feedback Docker image v2'

                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" build -t balaji00002/student-feedback:v2 .'
            }
        }


        stage('Test Application') {
            steps {

                echo 'Testing Docker image'

                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" images balaji00002/student-feedback:v2'
            }
        }


        stage('Push Image') {
            steps {

                echo 'Pushing Docker image v2 to Docker Hub'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%'

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" push balaji00002/student-feedback:v2'
                }
            }
        }


        stage('Deploy to Kubernetes') {
            steps {

                echo 'Deploying Student Feedback v2 to Kubernetes'

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

                echo 'Checking Kubernetes Deployment'

                withCredentials([
                    file(
                        credentialsId: 'kubeconfig',
                        variable: 'KUBECONFIG'
                    )
                ]) {

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get deployment student-feedback'

                    echo 'Checking all three Student Feedback Pods'

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get pods'

                    echo 'Checking Student Feedback NodePort Service'

                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get service student-feedback-service'
                }
            }
        }
    }


    post {

        success {
            echo 'Student Feedback v2 CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}