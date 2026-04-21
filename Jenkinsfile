pipeline {
    agent any

    parameters {
        choice(
            name: 'SERVICE',
            choices: ['flask', 'node'],
            description: 'Choose service to deploy'
        )
    }

    environment {
        DOCKERHUB_USER = "oriyahazan"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    def service = params.SERVICE
                    def tag = env.BUILD_NUMBER
                    def imageName = "${DOCKERHUB_USER}/${service}:${tag}"

                    sh "docker build -t ${imageName} ./${service}"
                }
            }
        }

        stage('Tag Image') {
            steps {
                script {
                    def service = params.SERVICE
                    def tag = env.BUILD_NUMBER

                    def versionedImage = "${DOCKERHUB_USER}/${service}:${tag}"
                    def latestImage = "${DOCKERHUB_USER}/${service}:latest"

                    sh "docker tag ${versionedImage} ${latestImage}"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    def service = params.SERVICE
                    def tag = env.BUILD_NUMBER

                    sh "docker push ${DOCKERHUB_USER}/${service}:${tag}"
                    sh "docker push ${DOCKERHUB_USER}/${service}:latest"
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                script {
                    sh """
                    ansible-playbook ansible/deploy-playbook.yml \
                    --extra-vars "service=${params.SERVICE} tag=${env.BUILD_NUMBER}"
                    """
                }
            }
        }
    }
}
