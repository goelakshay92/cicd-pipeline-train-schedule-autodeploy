pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "goelakshay92/train-schedule"
    }
    stages {
        stage('Build') {
            when {
                branch 'master'
            }
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }        
        stage('DeployToK8s') {
            steps {
                withKubeConfig([credentialsId: 'jenkins-deploy', serverUrl: 'https://172.31.6.52:6443']) {
                 sh 'kubectl apply -f train-schedule-service.yml'
                 sh 'kubectl apply -f train-schedule-deployment.yml'
                }
            }
        }
    }
}
