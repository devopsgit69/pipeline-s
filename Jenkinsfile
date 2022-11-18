pipeline {
    agent any
    stages {
        stage ('scm checkout') {
            steps {
                git 'https://github.com/Venkynarla/jenkins-docker-ansible.git'
            }
        }
        stage ('mvn build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage ('docker image build') {
            steps {
                sh "docker build -t venkatnarla3/taskapp:${env.BUILD_ID} ."
            }
        }
        stage ('docker image push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'passwd', usernameVariable: 'user')]) {
                sh "docker login -u ${user} -p ${passwd}"
                    }
                sh "docker push venkatnarla3/taskapp:${env.BUILD_ID}"
            }
        }
        stage ('deploy container using ansible') {
            steps {
                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e env.BUILD_ID=${env.BUILD_ID}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}
