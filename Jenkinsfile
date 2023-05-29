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
                kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJek1EVXhOakE0TkRReE4xb1hEVE16TURVeE16QTRORFF4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTGwrCkE3T0NSVXAyK0tLVzFaNHJwMWZNSHduWDRiWjFldHNFeWQrV1VjbDBrYWJkYjlzdDlvQlppeHJNYXdEVzdidWYKZUsxQ3lyRHI2M1E1WWcwTUVvUUhXRFFqK2NPUTdBMnJCd2lYZis5K0FtWlV2RDFYMTkwU1g1V1J3Y3hDRGtmZwp2eEdSa0JhcWgvQWxUNGVHazlwbTRlNGRLd3JPOVlqWk9iYTYwTXdjTituS2w0TGwzYzE0Tm5Cb1A2NHZBZkp2Cm5SRXkzR1I0STVRa2tUdXlSZjJONTdubUlGem5UdE1MU0IxclArMlNLdlN6MFdYNnFldTJQV2wyb0V4NzhITkUKZnRCcWxmZTRaSVpZR1EwV1NhZjBaOC9XVkt5aW1BelZrZjJhMWtwaHphRnZTcm8zdmJyektONisrVTFmMWUwMApyVkdBZ2VBU25qclloLzhJTWlNQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZQSWZEZnRDM0htZ0t3MDFrcjNrUHUydml2Q2VNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBQlVwbzYzR2U4bnNZVURLb0JwUgpuck9nYUZtQ1duaVIrczhVZGlRbTNoUnBVWjVxbk9RdVhaK1V5TEJTZHpGdnk2aG8rdEhiZ1lXbzZBTlJVZkZDCkRId0lvb0p4bTlrSk4rdG1UTjJiVHVwVDRKQmxkMmpaQWdycWozWms0Rzh3R2wxbnJmOEcxVHMzMyt0S0x6Z3MKdklDd21pRkJCUHpUS1U5Z2luTWFHZzI2U0Npc0tucWxOT1QxUStPVVBseEw4UFJmbzdIcFZ0L2lsd3pVUnRHUAp4bTJJTU1SWkNtb1lUbDdhRHV1QnFncHZQUWNxRENmRC9jYnQxdE5hZitCdUlGWGVBMjRuUnpmMEdtUmxua1VaCkFDSTcxdllWNHRMQk4ydGhyck15dlo4anNyTGY2N0p1bW5HSjlBMmJJN3hDWlRuK203enVVZUhNa3VYRExBS3EKSFpVPQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', credentialsId: 'kubernetes-admin', serverUrl: 'https://172.31.6.52:6443') {
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
