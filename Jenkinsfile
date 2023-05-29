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
                kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb3dJQkFBS0NBUUVBM1B5L3pDNm9LcVNkUHRCOU81TkR4MUVpRVovT3lWZ3F2RjMyVHVFODdyRGM3OVZaCko2OXFNV0dqNWdiV0Zna0tJTFBzQTN5bEU3ejNUejU0MzNMS0wwd3ErVlgwL1RqVjV2NEpzdnIzVGh2VnArRG4KdkhlVEtqVTAwZ0FOSnFCS0pTMjBiOFhNUGtDY0VGSHU5SE5QZVRxYnBRTm5QbWVUYjM1d0pFS3pOQlc1OUpmSwpyaHZDMzhUQkZWaER2SnlaaFl2NEFaS0xBMThZTmM0VHNYZlVXdUlzQ0Z0NW1UOEg0c1BlVVN4VGhISjl0ZGJGClRLY1ozb3p3alJURDRCZlVYc2JEdDNQdDRhb0xIemd6ektSbEhqZ2RHbXNENUt3bVIrUzVjNjVGRTdCS2hna1EKSFcxeEpnVWpiSkdjTXNlOHN1b3ovZU4zTWtWNWNPczZVL1Q5U1FJREFRQUJBb0lCQUMzb3VEUEQ4WjNDU3dTVQpyWitseld2bitBcnZSemRmQUtuWFgyY3R0Y25rcGxHUTRnK3ppbjB0eXEzcjk2Zm1RdHRlWllhbjYyZDVWMnQ0CkVBWmp3NHNsNkx1WjF1YVFXeWU4Mk9qODZFbXEwSzBxdFJuYlFaQ0FNelA1WE1JTWhMcktqdlMxZytQZTZrMGQKcUdYU1dzMVZscVltV2RtR0RRUVpvVlIzSXVHTHl4Y2VrckZkQlkrdVVsMFBWR1hZN1RHLzhONW9kL0hHV3RELwo0R3RwWXd4OVhjRVR5Z0pTZC9kWXMrRFJBNlhOTHExNDdvMEdsYjRHT0FOVnlFd3E0b0JUcldVTzVIeUI2S0JKClVUUC9iZkp0TDYyV1Y1R2htcTg1UERJUThYV3hVT1VRZWNOOXRuZi9DWExXcDUvd3FDRGg5SlBQdDRvOVNJb0sKbCtYc0ZTMENnWUVBODBMNXY0OTRjYy95WDlab3RHMEZCOW1lc0tEa2JnbGprNnkvMkQ0Ym9hNnlJZ0N1c2tuNgpRZ0VFVkl0K0xnSGNmdTg2TCtVTmtLQktva3V1RkQxWUo3RURDaDlFMDloSE5OWVdRUytuQWpSa3E4d2hsVnRSCk9XbEF1c0llT3o3Y1l6djl5NU5mdHdBaGxPL0pJTlQ5WHlZNys2bmFFdVdJUEE0RW1sRE5VTGNDZ1lFQTZJOHQKVGpEQUd1cFV5NUdGL2dtRzJrR21wTmI3SDI5aHFZT0lpVmhjN0M3cWFHbGgvNHYwZjVtNGp1QjFHK0wwRTB2UgpablIxVTRmdDE4VEhzbE5TZ3dIL2FRZU1Lay8yM25GQ1FyQTdZVll2RmQ5QmlCdmMvejVGNnFtNElvYTVwTXVnCk9nZDFrMkhTbTE2ajY2b01vQXQ4S0luT29HQWcwdE1tcXlKR0lmOENnWUJNVW8wdFR3UkpJU2M0Ym5yM2NPVU4KSzNnZ2pycGMwOUZNT2YwUENaMTl3dVBjeXo0RWJqVjRrLzBaMFpKTE9RWkhSU09LZDJFa0tVYUJXU0ZLOUZ3KwpSOGxlV2FVc21rVHRET1o2bVVyYTVEbFp0WGdyOWpTcndKd3dmRXNqNTNzYUgyNW5ldHVpL1FXTVJ4ZGhsN0hQCk1wM0xQZVZQM0hnNzcySCtVSys2VFFLQmdGRVVnZ1l0eHZ4YlVKNzhxSXV1Uko3Wm5zRC8xdVBaMEdIcVl2TUgKcExZUGtWZy96WlByOXAxNk9iZE9tR1hWbGFVWW5QUXNqQzlJUitzZlIwUnIrbXVFaWNqVGdkSEt1VlpLaElCOQpzdVkzeHdqSUlxL1NqeFZLWDlQMXdpWC9kTC80NG03NDVNd3lvZHZiZmlrT3k0SU5Id2QvTEZRTHo4WFJLajBtCklPOS9Bb0dCQUp5QmVzVkt1aGxCTHRMbTRvckJxajY1QUR0Q01wbUxwQUl3RHh1QWt6MDYycDVyRVZYNkIyc0YKNTN2QjZReWRLUHN5Y3crSi9mZ2VOcXJkYitlYW9IZmdEVFc5eklQSjQ5MmZRSTQwbWlaaWxyZkE2YWYycm1FTAp3SlliMUQ1WWNFTmZaeDRoZ3NkcVVlZ2JjTmo2TXZFN3RxanFENTJBUW5mdVc5WEROdHhaCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==', credentialsId: 'kubernetes-admin', serverUrl: 'https://172.31.6.52:6443') {
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
