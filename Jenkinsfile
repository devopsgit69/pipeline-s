pipeline {
	    agent any
	    environment {
	        DOCKER_TAG = "${env.BUILD_ID}"
	    }
	    tools {
	        maven 'maven'
	    }

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
	                sh "docker build -t venkatnarla3/taskapp:${DOCKER_TAG} ."
	            }
	            post {
	                success {
	                    sh 'docker images ls'
	                }
	            }
	        }
	        stage ('docker image push') {
	            steps {
	               withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'passwd', usernameVariable: 'user')]) {
	                sh "docker login -u ${user} -p ${passwd}"
	                    }
	                sh "docker push venkatnarla3/taskapp:${DOCKER_TAG}"
	            }
	        }
	        stage ('deploy container using ansible') {
	            steps {
	                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
	            }
	            post {
	                success {
	                    sh 'docker container ps'
	                }
	            }
	        }
	    }
	}
	


