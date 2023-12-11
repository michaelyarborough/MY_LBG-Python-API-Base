pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        ssh -i ~/.ssh/id-rsa jenkins@10.154.0.47 << EOF
                        docker stop flask-app || echo "flask-app not running"
                        docker rm flask-app || echo "flask-app not running"
                        docker stop nginx || echo "nginx not running"
                        docker rm nginx || echo "nginx not running"
                        docker rmi michaelyarborough/python-api || echo "python-api Image does not exist"
                        docker rmi michaelyarborough/mynginx || echo "mynginx Image does not exist"
                        docker network create lbg_network || echo "lbg_network exists"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        ssh -i ~/.ssh/id-rsa jenkins@10.154.0.58 << EOF
                        docker stop flask-app || echo "flask-app not running"
                        docker rm flask-app || echo "flask-app not running"
                        docker stop nginx || echo "nginx not running"
                        docker rm nginx || echo "nginx not running"
                        docker rmi michaelyarborough/python-api || echo "python-api Image does not exist"
                        docker rmi michaelyarborough/mynginx || echo "mynginx Image does not exist"
                        docker network create lbg_network || echo "lbg_network exists"
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
                        echo "Build not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t michaelyarborough/python-api -t michaelyarborough/python-api:v${BUILD_NUMBER} .
                        docker build -t michaelyarborough/mynginx -t michaelyarborough/mynginx:v${BUILD_NUMBER} ./nginx
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
                        echo "Build not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                         sh '''
                            docker push michaelyarborough/python-api
                            docker push michaelyarborough/python-api:v${BUILD_NUMBER}
                            docker push michaelyarborough/mynginx
                            docker push michaelyarborough/mynginx:v${BUILD_NUMBER}
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
                        ssh -i ~/.ssh/id-rsa jenkins@10.154.0.47 << EOF
                        docker run -d --name flask-app --network lbg_network michaelyarborough/python-api 
                        docker run -d -p 80:80 --name nginx --network lbg_network michaelyarborough/mynginx
                        
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        ssh -i ~/.ssh/id-rsa jenkins@10.154.0.58 << EOF
                        docker run -d --name flask-app --network lbg_network michaelyarborough/python-api 
                        docker run -d -p 80:80 --name nginx --network lbg_network michaelyarborough/mynginx
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
                
                sh '''
                ssh -i ~/.ssh/id-rsa jenkins@10.154.0.47 << EOF
                docker run -d --name flask-app --network lbg_network michaelyarborough/python-api 
                docker run -d -p 80:80 --name nginx --network lbg_network michaelyarborough/mynginx
                '''
            }
        }
    }
        stage('CleanUp') {

            steps {
                script {
                if(env.GIT_BRANCH == 'origin/dev') {
                    sh '''
                    docker rmi michaelyarborough/python-api:v${BUILD_NUMBER}  
                    docker rmi michaelyarborough/mynginx:v${BUILD_NUMBER}
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