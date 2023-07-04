pipeline{

    agent any

    environment{
        
        DOCKERHUB_USERNAME = "pavan0077"
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}"+"/"+"${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }

    stages{

        stage('Cleanup workspace'){

            steps{
                script{
                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){

            steps{
                script{
                    git credentialsId: 'github',
                    url: 'https://github.com/pavankumar0077/gitops_argocd_project.git',
                    branch: 'main'
                }
            }
        }
        stage ('Build Docker Image'){

            steps{
                script{
                   docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Docker image'){
            steps{
                script{
                    docker.withRegistry('',REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('Delete docker images'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
    }
}   