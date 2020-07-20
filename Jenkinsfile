pipeline {
	agent {
		node { 
			label 'jenkins@muskegg' 
		}
	}
	environment {
		GPG_SECRET_KEY = credentials('gpg-secret-key')
	}

	stages {
		stage('Build') {
			steps {
				sh echo "Building.."
				checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '44ba137d-05cd-4f39-8d38-cf15e4c03ec3', url: 'git@github.com:muskeg/devops-resume.git']]]) 
			}

			steps {
				sh """
				gpg --batch --import $GPG_SECRET_KEY
				"""
			}

                        steps {
                                sh """
                                cd $WORKSPACE
                                git secret reveal
                                cat .env.prod
                                """
                        }	
        	}
        

		stage('Deploy') {
			steps {
				sh echo "Deploy.."
			}
		}
	}
	
	post { 
		always { 
			cleanWs()
		}
	}
}
