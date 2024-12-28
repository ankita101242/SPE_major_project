pipeline {
    agent any
    environment {
        GITHUB_REPO_URL = 'https://github.com/AnkitaAgrawal12/SPE_Major_Project.git'
    }

    stages {
        stage('Cleanup') {
            steps {
                script {
                    sh 'docker rm -f prosepetals-frontend || true'
                    sh 'docker rm -f prosepetals-backend || true'
                    sh 'docker rm -f database-container || true'
                }
            }
        }

        stage('Checkout') {
            steps {
                script {
                    git branch: 'main', url: "${GITHUB_REPO_URL}"
                }
            }
        }

        stage('Maven Build') {
            environment {
                MVN_HOME = tool 'mvn'
            }
            steps {
                dir('./BACKEND/ProsePetal') {
                    sh "${MVN_HOME}/bin/mvn clean install"
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    dir('./BACKEND/ProsePetal') {
                        docker.build("ankitaagrawal12/prosepetals-backend", '.')
                    }
                    dir('./FRONTEND') {
                        docker.build("ankitaagrawal12/prosepetals-frontend", '.')
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    docker.withRegistry('', 'DockerHubCred') {
                        sh 'docker push ankitaagrawal12/prosepetals-frontend:latest'
                        sh 'docker push ankitaagrawal12/prosepetals-backend:latest'
                    }
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                ansiblePlaybook(
                    playbook: 'Playbook.yml',
                    inventory: 'Inventory'
                )
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
