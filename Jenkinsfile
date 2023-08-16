pipeline {
    agent {
        node {
            label 'docker'
        }
    }
    environment {
        registry = "brittanysaic/appfac1_repo"
        registryCredential = credentials('credential-id')
        githubCredential = credentials('credential-id')
    }
    stages {
        stage('init') {
            steps {
                library 'docker'
            }
        }
        stage('checkout') {
            steps {
                git branch: 'main',
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
                sh 'docker.image(img).remove(force: true)'
                sh 'docker rm ${JOB_NAME}'
            }
        }
        stage('Build Image') {
            steps {
                script {
                    img = "${registry}:${env.BUILD_ID}"
                    println("${img}")
                    docker.build(img: img)
                }
            }
        }
        stage('Push To DockerHub') {
            steps {
                script {
                    docker.withRegistry(url: 'https://registry.hub.docker.com', credentialsId: registryCredential) {
                        dockerImage.push("${img}")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "docker run -d --rm --name ${JOB_NAME} -p 5000:5000 ${img}"
            }
        }
    }
}
