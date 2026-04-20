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
                    def imageName = "${DOCKERHUB_USER}/${service}:latest"

                    sh "docker build -t ${imageName} ./${service}"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    def service = params.SERVICE
                    def imageName = "${DOCKERHUB_USER}/${service}:latest"

                    sh "docker push ${imageName}"
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                script {
                    sh """
                    ansible-playbook ansible/deploy-playbook.yml \
                    --extra-vars "service=${params.SERVICE}"
                    """
                }
            }
        }
    }
}
