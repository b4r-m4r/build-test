pipeline {
    environment {
        DOCKER_HOST = "127.0.0.1"
    }
    agent any

    stages {
        stage('Pull Images') {
            steps {
                sh "docker pull minipuppeteer/nginx-proxy:latest"
                sh "docker pull minipuppeteer/flask:latest"
                
            }
        }

        stage('Run Containers') {
            steps {
                sh "docker network create task_net || true"
                sh "docker run --name webapp --network task_net -d minipuppeteer/flask:latest"
                sh "docker run --name reverse-proxy --network task_net -d -p 80:80 minipuppeteer/nginx-proxy:latest"
            }
        }


        stage('Test Request') {
            steps {
                script {
                    def response = sh(script: "curl -s -o /dev/null -w '%{http_code}' http://localhost:80", returnStdout: true).trim()
                    
                    if (response != '200') {
                        error "Test failed: Reponse Code ${response}"
                    }
                    else {
                        echo "Test passed"
                    }
                }
            }
        }

        stage('Cleanup') {
            steps {
                    sh "docker stop flask_container nginx_container || true"
                    sh "docker rm flask_container nginx_container || true"
                    sh "docker network rm task_net || true"
            }
        }
    }
}
