pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id-rsa jenkins@10.154.0.47 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                docker stop nginx || echo "nginx not running"
                docker rm nginx || echo "nginx not running"
                docker rmi michaelyarborough/python-api || echi "python-api Image does not exist"
                docker rmi michaelyarborough/mynginx || echi "mynginx Image does not exist"
                docker network create lbg_network || echo "lbg_network exists"
                '''
           }
        }
    stage('Build') {
            steps {
                sh '''
                docker build -t michaelyarborough/python-api -t michaelyarborough/python-api:v${BUILD_NUMBER} .
                docker build -t michaelyarborough/mynginx -t michaelyarborough/mynginx:v${BUILD_NUMBER} ./nginx
                '''
           }
        }
         
        stage('Push') {
            steps {
                sh '''
                docker push michaelyarborough/python-api
                docker push michaelyarborough/python-api:v${BUILD_NUMBER}
                docker push michaelyarborough/mynginx
                docker push michaelyarborough/mynginx:v${BUILD_NUMBER}
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id-rsa jenkins@10.154.0.47 << EOF
                docker run -d --name flask-app michaelyarborough/python-api 
                docker run -d -p 80:80 --name nginx --network lbg_network michaelyarborough/mynginx

                '''
            }
        }
        stage('CleanUp') {

            steps {
                sh '''
                docker system prune -f
                docker rmi michaelyarborough/python-api  
                docker rmi michaelyarborough/mynginx 
                '''
            }

        }
    }
}
