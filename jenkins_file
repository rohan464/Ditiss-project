pipeline {
    agent any

    stages {
        stage('1 Git clone') {
            steps {
                sh 'rm -rf Project'
                git branch: 'main', url: 'https://github.com/Shubhs16/Scan_webapp.git'
            }
        }
        stage('2 Sonar scanner') {
            steps {
                sh 'sonar-scanner -X -Dsonar.projectKey=scan_webapp -Dsonar.sources=. -Dsonar.host.url=http://13.232.18.53:9000 -Dsonar.login=admin -Dsonar.password=root'
            }
        } 
        stage('3 Docker Build images') {
            steps {
                sh 'sudo docker stop mywebapp'
                sh 'sudo docker rm mywebapp'
                sh 'sudo docker image build -t web:v1.$BUILD_ID .'
                sh 'sudo docker image tag web:v1.$BUILD_ID shubhamsk18/scan-webapp:latest'
                sh 'sudo docker run -d --name mywebapp -p 9990:80 shubhamsk18/scan-webapp:latest'
            }
        }
	stage('4 OWASP ZAP') {
            steps {
                    sh 'sudo docker stop zap'
                    sh 'sudo docker rm zap'
                    sh 'sudo docker run --name zap -v $(pwd):/zap/wrk/:rw -t owasp/zap2docker-stable zap-full-scan.py -I -t http://192.168.56.185:9990/login.php -r scan_report.html'
              	    echo 'OWASP ZAP scan success!!!'
            }
        }
        stage('5 Push image on Dockerhub') {
            steps {
               withCredentials([usernamePassword(credentialsId: 'dockerhub_token', passwordVariable: 'password', usernameVariable: 'user_name')]) {
                    sh 'sudo docker login -u ${user_name} -p ${password}'
                    sh 'sudo docker image push shubhamsk18/scan-webapp:latest'
                }
            } 
        }
    }
 }
