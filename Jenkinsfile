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
                        docker build -t gcr.io/lbg-mea-16/michaely-project-flask-api:latest -t gcr.io/lbg-mea-16/michaely-project-flask-api:prod-v${BUILD_NUMBER} .
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t gcr.io/lbg-mea-16/michaely-project-flask-api -t gcr.io/lbg-mea-16/michaely-project-flask-api:dev-v${BUILD_NUMBER} .
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
                        docker push gcr.io/lbg-mea-16/michaely-project-flask-api
                        docker push gcr.io/lbg-mea-16/michaely-project-flask-api:prod-v${BUILD_NUMBER}
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                         sh '''
                            docker push gcr.io/lbg-mea-16/michaely-project-flask-api
                            docker push gcr.io/lbg-mea-16/michaely-project-flask-api:dev-v${BUILD_NUMBER}
                          
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
                        kubectl set image deployment/flask-api-deployment flask-container=gcr.io/lbg-mea-16/michaely-project-flask-api:prod-v${BUILD_NUMBER} -n prod
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl apply -n dev -f ./kubernetes
                        kubectl set image deployment/flask-api-deployment flask-container=gcr.io/lbg-mea-16/michaely-project-flask-api:dev-v${BUILD_NUMBER} -n dev
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
                    docker rmi gcr.io/lbg-mea-16/michaely-project-flask-api:prod-v${BUILD_NUMBER}
                                        '''
                } else if(env.GIT_BRANCH == 'origin/dev') {
                    sh '''
                    docker rmi gcr.io/lbg-mea-16/michaely-project-flask-api:dev-v${BUILD_NUMBER} 
                    '''
                }
            }
            sh '''
            docker system prune -f
            '''
               
            }
        }
    }
}