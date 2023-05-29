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
                    // app.inside {
                    //    sh 'echo Hello, World!'
                    // }
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
                kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURJVENDQWdtZ0F3SUJBZ0lJVW9IWDgwYTNRWnN3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TXpBMU1UWXdPRFEwTVRkYUZ3MHlOREExTVRVd09EUTBNakJhTURReApGekFWQmdOVkJBb1REbk41YzNSbGJUcHRZWE4wWlhKek1Sa3dGd1lEVlFRREV4QnJkV0psY201bGRHVnpMV0ZrCmJXbHVNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDQVFFQTNQeS96QzZvS3FTZFB0QjkKTzVORHgxRWlFWi9PeVZncXZGMzJUdUU4N3JEYzc5VlpKNjlxTVdHajVnYldGZ2tLSUxQc0EzeWxFN3ozVHo1NAozM0xLTDB3cStWWDAvVGpWNXY0SnN2cjNUaHZWcCtEbnZIZVRLalUwMGdBTkpxQktKUzIwYjhYTVBrQ2NFRkh1CjlITlBlVHFicFFOblBtZVRiMzV3SkVLek5CVzU5SmZLcmh2QzM4VEJGVmhEdkp5WmhZdjRBWktMQTE4WU5jNFQKc1hmVVd1SXNDRnQ1bVQ4SDRzUGVVU3hUaEhKOXRkYkZUS2NaM296d2pSVEQ0QmZVWHNiRHQzUHQ0YW9MSHpnegp6S1JsSGpnZEdtc0Q1S3dtUitTNWM2NUZFN0JLaGdrUUhXMXhKZ1VqYkpHY01zZThzdW96L2VOM01rVjVjT3M2ClUvVDlTUUlEQVFBQm8xWXdWREFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEVlIwbEJBd3dDZ1lJS3dZQkJRVUgKQXdJd0RBWURWUjBUQVFIL0JBSXdBREFmQmdOVkhTTUVHREFXZ0JUeUh3MzdRdHg1b0NzTk5aSzk1RDd0cjRydwpuakFOQmdrcWhraUc5dzBCQVFzRkFBT0NBUUVBZDlrU0lWUWF0eTkxLytBTUhsR3RUSEhwaEwyL2ZQT3dRU1JMCnRJYnYxM0Y4TUhKK3RzL3VYL2xoWHlhSWxlTXpiK3hyWVJxWkZwcVBBeFBDMDVKZW84NTI4VEk5cXcxZG9IK2wKazF2WHZXMlRYa01MczZHRGE1MWlqV2VKdmRLdzJXbWM3VnMrL252ZGpodmdwZmlyNjV0aVdVZnBZYzBuZCtUZAo5ZWtSTzFGM2cxOUM0THBsb2JWM1VWN3RRKy9JeU1RSnpNQVFqTHlGRS9STWpjeDRYTGt0MGtUZmpETGFLeFVyCjZyL0w1eFdQTWtMSHcyS3NoSG1OSEhpelFrR3o2ZUFKQzhxR1F5RXkyaEVoVURZNDhDRUFHVVlDVkpqYzQwZVEKcG5xUnlmK2lNZnR1N25EelBqL21Ga0hYYnU4VFhVYTFPRzhvbFJEZS9LLy8vL3U1NWc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', credentialsId: 'kubernetes', serverUrl: 'https://172.31.6.52:6443') {
                 // some block
                 sh 'kubectl apply -f train-schedule-deployment.yaml'
                 sh 'kubectl apply -f train-schedule-app-service.yaml'
                 sh 'kubectl rollout restart deployment train-schedule'
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-service.yml',
                    enableConfigSubstitution: true
                )
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-deployment.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}
