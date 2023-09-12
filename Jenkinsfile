pipeline {
    agent any

    environment {
        // Assign the repository name to a variable
        REPO_NAME = "ishank006/nginx"
    }

    stages {
        stage('cloning') {
            steps {
                // Checkout code from source control
                checkout scm
            }
        }
        
        stage('Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_CREDENTIALS_USR', passwordVariable: 'DOCKERHUB_CREDENTIALS_PSW')]) {
                        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    }
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    sh "docker build -t $REPO_NAME:${GIT_COMMIT[0..6]} ."
                }
            }
        }
        
        stage('Push') {
            steps {
                sh "docker push $REPO_NAME:${GIT_COMMIT[0..6]}"
            }
        }
        
        stage('Pass image to cdbuild') {
            steps {
                script {
                    build job: 'nginx_cd_1', parameters: [
                        string(name: 'IMAGE_TAG', value: "${GIT_COMMIT[0..6]}"),
                        string(name: 'REPOSITORY_NAME', value: "$REPO_NAME")
                    ]
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}

