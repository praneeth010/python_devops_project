pipeline {
    agent any

    environment {
        IMAGE_NAME = "praneeth0108/python-devops-app"
        TAG = "${BUILD_NUMBER}"
        GIT_REPO = "https://github.com/praneeth010/python_Application_Argocd.git"
        GIT_BRANCH = "main"
    }

    stages {

        stage('Clone App Repo') {
            steps {
                git 'https://github.com/praneeth010/python_devops_project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'praneeth0108',
                    passwordVariable: 'Praneeth@0108'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$TAG
                    '''
                }
            }
        }

        stage('Update ArgoCD Repo') {
            steps {
                dir('argocd-repo') {
                    git branch: "${GIT_BRANCH}", url: "${GIT_REPO}"

                    sh """
                    sed -i 's|image: .*|image: $IMAGE_NAME:$TAG|' deployment.yaml
                    """

                    withCredentials([usernamePassword(
                        credentialsId: 'github-creds',
                        usernameVariable: 'ratnalapraneeth@gmail.com',
                        passwordVariable: 'Praneeth@0108'
                    )]) {
                        sh '''
                        git config user.email "ratnalapraneeth@gmail.com"
                        git config user.name "praneeth010"

                        git add .
                        git commit -m "Update image to $IMAGE_NAME:$TAG" || true

                        git push https://$GIT_USER:$GIT_PASS@github.com/praneeth010/python_Application_Argocd.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}