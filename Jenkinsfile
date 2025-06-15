pipeline {
    agent any

    tools {
        maven 'Maven3'       // Maven configured in Global Tool Configuration
    }

    environment {
        DOCKER_HOST = 'tcp://192.168.1.14:2375'
        IMAGE_NAME = "venkatesansg111/abctechnologies"
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
                sh docker version
                sh 'cp target/ABCtechnologies-1.0.war .'  
                sh "docker build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} ."  
            }
        }
        stage('Archive WAR') {
            steps {
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }
    }
}