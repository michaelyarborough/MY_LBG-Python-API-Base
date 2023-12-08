pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id-rsa jenkins@10.154.0.47 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                '''
           }
        }
    stage('Build') {
            steps {
                sh '''
                docker build -t michaelyarborough/python-api -t michaelyarborough/python-api:v${BUILD_NUMBER} .
                '''
           }
        }
         
        stage('Push') {
            steps {
                sh '''
                docker push michaelyarborough/python-api 
                docker push michaelyarborough/python-api:v${BUILD_NUMBER}
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id-rsa jenkins@10.154.0.47 << EOF
                docker run -d -p 80:8080 --name flask-app michaelyarborough/python-api 

                '''
            }
        }
        stage('CleanUp') {

            steps {

                sh 'docker system prune -f'
                
            }

        }
    }
}
