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
				currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
			}
		}
	}
}
