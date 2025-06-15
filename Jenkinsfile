pipeline {
    agent any

    tools {
        maven 'Maven3'       // Maven configured in Global Tool Configuration
    }

    environment {
        DOCKER_HOST = 'tcp://192.168.1.14:2375'
        IMAGE_NAME = "venkatesansg111/abctechnologies"
        CONTAINER_NAME = 'abctechnologies-container'
        GIT_REPO = 'https://github.com/venkatesansg111/ABC_Project.git'
        BRANCH = 'main'
    }
    stages {
        stage('Code Checkout') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }
        stage('Code Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Code Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Code Package') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Check Directory') {
            steps {
                sh 'pwd'
                sh 'ls -l'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker version'
                sh 'cp target/ABCtechnologies-1.0.war .'  
                sh "docker build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} ."  
            }
        }
        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'docker-registry-credentials', url: '']) {
                    sh """
                        docker tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${IMAGE_NAME}:latest
                        docker push ${IMAGE_NAME}:${env.BUILD_NUMBER}
                        docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }
        stage('Deploy Docker Container') {
            steps {
                sh """
                    echo "Cleaning up any existing container..."
                    docker rm -f \$(docker ps -aq -f "name=${CONTAINER_NAME}") 2>/dev/null || true

                    echo "Starting new container..."
                    docker run -itd -p 8081:8080 --name "${CONTAINER_NAME}-${BUILD_NUMBER}" ${IMAGE_NAME}:latest
                """
            }
        }
        stage('Archive WAR') {
            steps {
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }
    }
}