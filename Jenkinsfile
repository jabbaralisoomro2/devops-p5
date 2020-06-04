pipeline {
	agent any
	stages {

		stage('Lint HTML') {
			steps {
				sh 'tidy -q -e ./index.html'
			}
		}
		
		stage('Build Docker Image') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					echo '''
						build docker image
					'''
					sh '''
						docker build -t ajpm1977/capstone-devops .
					'''					
				}
			}
		}

		stage('Push Image To Dockerhub') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					echo '''
						push image to dockerhub
					'''
					sh '''
						docker push ajpm1977/capstone-devops
					'''					
				}
			}
		}

		stage('Create AWS kubernetes cluster') {
			steps {
				withAWS(region:'us-east-1', credentials:'aws-static') {
					echo '''
						Crear kubernetes cluster
					'''
					sh '''

						eksctl create cluster \
						--name capstonecluster \
						--version 1.13 \
						--nodegroup-name workers \
						--node-type t2.small \
						--nodes 2 \
						--nodes-min 1 \
						--nodes-max 3 \
						--node-ami auto \
						--region us-east-1 \
						--zones us-east-1a \
						--zones us-east-1b \
						--zones us-east-1c \
					'''					
				}
			}
		}

		stage('Create config file for cluster') {
			steps {
				withAWS(region:'us-east-1', credentials:'aws-static') {
					echo '''
						crear config file para el cluster
					'''
					sh '''
						aws eks --region us-east-1 update-kubeconfig --name cluster-ajpm
					'''									
				}
			}
		}

		stage('Set current kubectl context') {
			steps {
				withAWS(region:'us-east-1', credentials:'aws-static') {
					echo '''
						asigno el contexto AWS a kubectl
					'''
				   sh '''
						kubectl config use-context arn:aws:eks:us-east-1:082521614617:cluster/cluster-ajpm
					'''					
				}
			}
		}

        stage ('Green deployment to AWS Loadbalancer') {
            steps {
               withAWS(region:'us-east-1', credentials:'aws-static') {
                   echo '''
				   		subo desarrollo verde al balanceador de carga
				   '''
					sh 'kubectl apply -f green-deploy.yml'

               }
            }
        }

        stage ('Add blue deployment to AWS Loadbalancer') {
            steps {
               withAWS(region:'us-east-1', credentials:'aws-static') {
                   echo '''
				   		subo desarrollo azul al balanceador de carga
				   '''
				   sh 'kubectl apply -f blue-deploy.yml'
               }
            }
        }

        stage ('Remove blue deployment from AWS Loadbalancer') {
            steps {
               withAWS(region:'us-east-1', credentials:'aws-static') {
                   echo '''
				   		borro desarrollo azul del balanceador de carga
				   '''
				   sh 'kubectl delete deploy/deployment-blue'
               }
            }
        }

        stage ('Remove green deployment from AWS Loadbalancer') {
            steps {
               withAWS(region:'us-east-1', credentials:'aws-static') {
                   echo '''
				   		borro desarrollo verde del balanceador de carga
				   '''
				   sh 'kubectl delete deploy/deployment-green'
               }
            }
        }
    }
}