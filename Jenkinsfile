pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rk4027-N/Project_2.git'
            }
        }

        stage('Terraform Apply') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-devops-creds']]) {
                    dir('terraform') {
                        sh 'terraform init'
                        sh 'terraform plan'
                        sh 'terraform apply -auto-approve'
                    }
                }
            }
        }

        stage('Ansible Deploy') {
            steps {
                sh 'ansible-playbook -i terraform/hosts ansible/deploy.yml'
            }
        }

        stage('Docker Build and Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                    sh 'docker build -t my-web-app .'
                    sh 'docker tag my-web-app mydockerhubuser/my-web-app:latest'
                    sh 'docker push mydockerhubuser/my-web-app:latest'
                }
            }
        }

        stage('Test and Deploy') {
            steps {
                dir('terraform') {
                    script {
                        def publicIp = sh(script: 'terraform output -raw instance_public_ip', returnStdout: true).trim()
                        sh "curl http://${publicIp}"
                    }
                }
            }
        }
    }
}
