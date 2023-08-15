def img
pipeline {
    environment {
        registry = "brittanysaic/appfac1_repo" //To push an image to Docker Hub, you must first name your local image using your Docker Hub username and the repository name that you created through Docker Hub on the web.
        registryCredential = 'ff849856-4d29-4a39-9419-10d80c0e4d00'
        githubCredential = '6f006dca-ddbc-4afa-aa6b-2bd81306f3d0'
        dockerImage = ''
    }
    agent any
    stages {      
        stage('checkout') {
                steps {
                git branch: 'master',
                credentialsId: 6f006dca-ddbc-4afa-aa6b-2bd81306f3d0,
                url: 'https://github.com/utmanbri/Pipeline_CICD.git'
                }
        }      
        stage ('Test'){
                steps {
                sh "pytest testRoutes.py"
                }
        }       
        stage ('Clean Up'){
            steps{
                sh returnStatus: true, script: 'docker stop $(docker ps -a | grep ${JOB_NAME} | awk \'{print $1}\')'
                sh returnStatus: true, script: 'docker rmi $(docker images | grep ${registry} | awk \'{print $3}\') --force' //this will delete all images
                sh returnStatus: true, script: 'docker rm ${JOB_NAME}'
            }
        }
        stage('Build Image') {
            steps {
                script {
                    img = registry + ":${env.BUILD_ID}"
                    println ("${img}")
                    dockerImage = docker.build("${img}")
                }
            }
        }
        stage('Push To DockerHub') {
            steps {
                script {
                    docker.withRegistry( 'https://registry.hub.docker.com ', ff849856-4d29-4a39-9419-10d80c0e4d00 ) {
                        dockerImage.push()
                    }
                }
            }
        }     
        stage('Deploy') {
           steps {
                sh label: '', script: "docker run -d --name ${JOB_NAME} -p 5000:5000 ${img}"
          }
        }

      }
    }