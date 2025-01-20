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
                sh "docker run --name webapp --network task_net -d --add-host=host.docker.internal:host-gateway minipuppeteer/flask:latest"
                sh "docker run --name reverse-proxy --network task_net -d -p 80:80 minipuppeteer/nginx-proxy:latest"
            }
        }


        stage('Test Request') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
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
        }
    }
}
