def img = ''

pipeline {
    environment {
        registry = "brittanysaic/appfac1_repo"
        registryCredential = credentials('ff849856-4d29-4a39-9419-10d80c0e4d00')
        githubCredential = credentials('6f006dca-ddbc-4afa-aa6b-2bd81306f3d0')
    }
    agent any
    stages {      
        stage('checkout') {
            steps {
                git branch: 'master',
                credentialsId: githubCredential,
                url: 'https://github.com/utmanbri/Pipeline_CICD.git'
            }
        }      
        stage('Test') {
            steps {
                sh "pytest testRoutes.py"
            }
        }       
        stage('Clean Up') {
            steps {
                sh 'docker stop $(docker ps -a | grep ${JOB_NAME} | awk \'{print $1}\')'
                sh 'docker rmi $(docker images | grep ${registry} | awk \'{print $3}\') --force'
                sh 'docker rm ${JOB_NAME}'
            }
        }
        stage('Build Image') {
            steps {
                script {
                    img = "${registry}:${env.BUILD_ID}"
                    println("${img}")
                    docker.build(img)
                }
            }
        }
        stage('Push To DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }     
        stage('Deploy') {
           steps {
                sh "docker run -d --name ${JOB_NAME} -p 5000:5000 ${img}"
           }
        }
    }
}
