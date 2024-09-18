pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'Github_LOGIN', 
                url: 'https://github.com/abhi-shek-2/Django-todo/',
                branch: 'main'
           }
        }

        stage('Build and Push Docker Image') {
            environment {
                DOCKER_IMAGE = "ranaabhi02/ultimate-cicd-e2e:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            steps {
                script {
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                        dockerImage.push()
                    }
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'Github_LOGIN', 
                url: 'https://github.com/abhi-shek-2/Django-todo/deploy.git',
                branch: 'main'
            }
        }
        
        stage('Update Deployment File') {
            environment {
                GIT_REPO_NAME = "CICD-Test"
                GIT_USER_NAME = "abhi-shek-2"
            }
            steps {
                withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                    sh '''
                        git config user.email "abhi.786.35@gmail.com"
                        git config user.name "Abhishek Rana"
                        BUILD_NUMBER=${BUILD_NUMBER}
                        sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" deploy/deplo.yml
                        git add deploy/deplo.yml
                        git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                        git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    '''
                }
            }
        }
    }
}
