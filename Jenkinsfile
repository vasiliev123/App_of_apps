def backendImage = "vasilievpl123/backend"
def frontendImage = "vasilievpl123/frontend"

pipeline {
	agent {
		label 'agent'
	}

	parameters {
		string(name: 'backendDockerTag', defaultValue: 'latest', description: 'Backend docker tag')
		string(name: 'frontendDockerTag', defaultValue: 'latest', description: 'Frontend docker tag')
	}

	stages {
		stage('Get code') {
			steps {
				checkout scm
			}
		}

		stage('Adjust Version') {
			steps {
				script {
					currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
				}
			}
		}

		stage('Clean containers') {
			steps {
				script {
					sh "docker rm -f frontend backend"
				}
			}
		}

		stage('Deploy application') {
			steps {
				script {
					withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
										"BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
						sh "docker compose up -d"
					}
				}
			}
		}
	}
	post {
			always {
				sh "docker compose down"
				cleanWs()
			}
    }
}
