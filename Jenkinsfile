def backendImage = "vasilievpl123/backend"
def frontendImage = "vasilievpl123/frontend"

pipeline {
	agent {
		label 'agent'
	}

	tools {
		terraform 'Terraform'
	}

	parameters {
		string(name: 'backendDockerTag', defaultValue: 'latest', description: 'Backend docker tag')
		string(name: 'frontendDockerTag', defaultValue: 'latest', description: 'Frontend docker tag')
	}

	environment {
		PIP_BREAK_SYSTEM_PACKAGES = 1
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
		stage('Selenium tests') {
			steps {
				sh "pip3 install -r test/selenium/requirements.txt"
				sh "python3 -m pytest test/selenium/frontendTest.py"
			}
		}

		stage('Run terraform') {
            steps {
                dir('Terraform') {                
                    git branch: 'main', url: 'https://github.com/vasiliev123/terraform'
                    withAWS(credentials:'AWS', region: 'us-east-1') {
                            sh 'terraform init -backend-config=bucket=grzegorz-was-panda-devops-core-18'
                            sh 'terraform apply -auto-approve -var bucket_name=grzegorz-was-panda-devops-core-18'
                            
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
