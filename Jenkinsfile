pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        kubectl create ns prod || echo "Prod NS already exists"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                     kubectl create ns dev || echo "Dev NS already exists"
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
		        }
            }
        }
        stage('Build') {
            steps {               
                 script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        docker build -t michaelyarborough/project-flask-api -t michaelyarborough/project-flask-api:prod-v${BUILD_NUMBER} .
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t michaelyarborough/project-flask-api -t michaelyarborough/project-flask-api:dev-v${BUILD_NUMBER} .
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
		        }
             }
        }   
        stage('Push') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        docker push michaelyarborough/project-flask-api
                        docker push michaelyarborough/project-flask-api:prod-v${BUILD_NUMBER}
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                         sh '''
                            docker push michaelyarborough/project-flask-api
                            docker push michaelyarborough/project-flask-api:dev-v${BUILD_NUMBER}
                          
                            '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
		        }
          }
        }
        stage('Deploy') {
            steps {
                 script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        kubectl apply -n prod -f ./kubernetes
                        kubectl set image deployment/flask-api-deployment flask-container=michaelyarborough/project-flask-api:prod-v${BUILD_NUMBER} -n prod
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl apply -n dev -f ./kubernetes
                        kubectl set image deployment/flask-api-deployment flask-container=michaelyarborough/project-flask-api:dev-v${BUILD_NUMBER} -n dev
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }    
            }
        }
    }
        stage('CleanUp') {

            steps {
                script {
                if(env.GIT_BRANCH == 'origin/main') {
                    sh '''
                    docker rmi michaelyarborough/project-flask-api:prod-v${BUILD_NUMBER}  
                                        '''
                } else if(env.GIT_BRANCH == 'origin/dev') {
                    sh '''
                    docker rmi michaelyarborough/project-flask-api:dev-v${BUILD_NUMBER}  
                    '''
                }
            }
            sh '''
            docker rmi michaelyarborough/project-flask-api:latest
            docker system prune -f
            '''
               
            }
        }
    }
}