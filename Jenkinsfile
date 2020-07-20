pipeline {
	agent none

	stages {
		stage('Clean up') {
                        agent {
                                node {
                                        label 'jenkins@muskegg'
                                }
                        }
			steps {
				cleanWs()
			}
		}

		stage('Checkout, env files') {
        		agent {
                		docker {
                        		image 'alpine:3.12'
                        		args '-u root:root --network host -v ${PWD}:/usr/src/app -w /usr/src/app'
                		}
        		}
			environment {
				GPG_SECRET_KEY = credentials('gpg-secret-key')
			}
			steps {
				checkout scm
				sh """
				echo "http://dl-cdn.alpinelinux.org/alpine/v3.12/main" >> /etc/apk/repositories
				echo "http://dl-cdn.alpinelinux.org/alpine/v3.12/community" >> /etc/apk/repositories
				echo "http://nl.alpinelinux.org/alpine/edge/main/" >> /etc/apk/repositories
				echo "http://nl.alpinelinux.org/alpine/edge/community/" >> /etc/apk/repositories
				echo "http://nl.alpinelinux.org/alpine/edge/testing/" >> /etc/apk/repositories
				apk update
				apk add gawk git git-secret
				gpg --batch --import $GPG_SECRET_KEY
				cd $WORKSPACE
				git secret reveal -f -p ''
				"""
                        }	
        	}
        

                stage('Build') {
			agent {
				node { 
					label 'jenkins@muskegg' 
				}
			}
                        steps {
				sh """
                                cd $WORKSPACE
                                docker-compose -f docker-compose.prod.yaml up -d --build
                                docker images
                                """
                        }
                }

		stage('Deploy') {
			steps {
				echo "Deploy.."
			}
		}
	}
	
}
