pipeline {
    agent any

    tools{
        maven 'Maven3'
    }
    environment {
        LOCAL_IMAGE_NAME="purvash/devops-image:latest"
        REGISTRY_NAME="purvash/devops-image:${BUILD_NUMBER}"
    }

    stages{
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Purvash-143/devops-project']])
                bat 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    bat "docker build -t ${LOCAL_IMAGE_NAME} ."
                    bat 'docker images'
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'dockerhubpass')]) {
                   bat "docker login https://registry.hub.docker.com --username purvash --password ${dockerhubpass}"
                   bat "docker tag ${LOCAL_IMAGE_NAME} ${REGISTRY_NAME}"
                   bat "docker push ${REGISTRY_NAME}"
}
                   
           }
             }
         }
        stage('Edit the yaml file'){
            steps{
                script{
                    bat """
                git clone https://github.com/Purvash-143/argocd-app-config.git
                cd argocd-app-config/dev
                
                
                SET text = readFile file: "deployment.yaml"
                SET text = text.replaceAll("%tag%", "${BUILD_NUMBER}") 
                    
                git config --global user.email "purvashgangolli@gmail.com"
                git config --global user.name "Purvash"

                git add . 
                git commit -m "Update app image tag to ${BUILD_NUMBER}"
                git push origin main
            """
                }
            }
        }
    }
}
