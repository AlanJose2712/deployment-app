pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout successful'
                // Jenkins automatically checks out the repository.
            }
        }

        stage('Build and Test') {
            steps {
                sh 'ls -ltr'
                sh 'python3 --version'
                sh 'pip3 --version'
                sh 'pip3 install -r requirements.txt'
                sh 'python3 -m py_compile app.py'
                echo 'FastAPI application build/test successful'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonarscanner'

                    withSonarQubeEnv('sonarqube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=deployment-app \
                            -Dsonar.sources=.
                        """
                    }
                }
            }
        }

        stage('Build and Push Docker Image') {
            environment {
                DOCKER_IMAGE = "alanjose2929/deployment-app:${BUILD_NUMBER}"
            }

            steps {
                script {
                    sh 'docker build -t ${DOCKER_IMAGE} .'

                    def dockerImage = docker.image("${DOCKER_IMAGE}")

                    docker.withRegistry(
                        'https://index.docker.io/v1/',
                        'docker-cred'
                    ) {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Update Deployment File') {
            environment {
                GIT_REPO_NAME = "deployment-app"
                GIT_USER_NAME = "AlanJose2712"
            }

            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'github',
                        usernameVariable: 'GIT_USER',
                        passwordVariable: 'GITHUB_TOKEN'
                    )
                ]) {
                    sh '''
                        git config user.email "alanjosep4949@gmail.com"
                        git config user.name "${GIT_USER_NAME}"

                        sed -i "s|image: .*|image: alanjose2929/deployment-app:${BUILD_NUMBER}|g" k8s/deployment.yaml

                        git add k8s/deployment.yaml

                        git commit -m "Update FastAPI image tag to ${BUILD_NUMBER} [skip ci]" || echo "No changes to commit"

                        git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME}.git HEAD:main
                    '''
                }
            }
        }
    }
}