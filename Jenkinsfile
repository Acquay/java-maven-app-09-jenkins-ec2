#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: SCMSourceRetriever(
    new GitSCMSource(
        credentialsId: 'gitlab-credentials',
        remote: 'https://gitlab.com/twn-devops-bootcamp/latest/09-aws/jenkins-shared-library.git'
    )
)

pipeline {   
    agent any
    tools {
        maven 'Maven-3.9.2'
    }

    environment {
        IMAGE_NAME = 'ztech101/nana-docker-image:java-maven-1.0'
    }
    
    stages {  
        stage("build app") {
            steps {
                script {
                    echo "Building the application jar..."
                    buildJar()
                }
            }
        }

        stage("build Image") {
            steps {
                script {
                    echo 'Building the Docker image...'
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                    echo 'Deploying Docker image to EC2...'
                    def dockerCmd = "docker run -p 8080:8080 -d ${env.IMAGE_NAME}"
                    sshagent(['ec2-server-key']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@44.211.197.52 ${dockerCmd}
                        """
                    }
                }
            }
        }               
    }
}
