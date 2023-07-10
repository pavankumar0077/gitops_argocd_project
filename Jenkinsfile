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
        stage('Updating kubernetes deployment'){
            steps{
                script{
                    sh """
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/s{APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml
                    """
                }
            }
        }
        stage('Push the changed deployment file to Git'){
            steps{
                script{
                    sh """
                    git config --global user.name "pavankumar0077"
                    git config --global user.email "dasarepavan007@gmail.com"
                    git add deployment.yml
                    git commit -m "updated the deployment.yml file"
                    cat deployment.yml
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                        sh "git push https://github.com/pavankumar0077/gitops_argocd_project.git main"
                    }
                }
            }
        }
    }
}
