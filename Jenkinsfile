pipeline {
    agent any
    environment {
        DOCKER_USER = 'nourelhoudaboudhina'
        IMAGE_NAME = 'timesheet-app'
        NAMESPACE = 'chap4'
    }
    stages {
        stage('GIT') {
            steps {
                git branch: 'master', url: 'https://github.com/NourelhoudaBoudhina/timesheet-project.git'
            }
        }
        stage('COMPILATION') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('INSTALLATION') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USER')]) {
                        sh "docker build -t nourelhoudaboudhina/timesheet-app:1.1 ."
                        sh "echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USER --password-stdin"
                        sh "docker push nourelhoudaboudhina/timesheet-app:1.1"
                    }
                }
            }
        }
        stage('DEPLOIEMENT') {
            steps {
                withEnv(["KUBECONFIG=/var/lib/jenkins/.kube/config"]) {
                    sh "kubectl set image deployment/timesheet-dep timesheet=nourelhoudaboudhina/timesheet-app:1.1 -n chap4"
                    sh "kubectl rollout status deployment/timesheet-dep -n chap4"
                }
            }
        }
    }
}