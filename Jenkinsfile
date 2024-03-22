pipeline {
    agent any
    
    tools{
        jdk'jdk8'
        nodejs'node17'
    }

    stages {
        stage('Cloning From GIT') {
            steps {
                git 'https://github.com/venkatesh9691/docker-react.git'
            }
        }
        stage("NPM Dependencies"){
            steps{
                sh'npm install'
            }
        }
        stage('NPM Testing') {
            steps {
                sh 'npm test'
            }
        }
        stage("NPM build"){
            steps{
                sh'npm run build'
            }
        }
        stage("file system scanning"){
            steps{
	            sh'trivy fs --format table -o trivy-fs-report.html .'
            }
        }
        stage("OWASP Dependency Checking"){
            steps{
                dependencyCheck additionalArguments: ' --scan ./', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }
        stage("publish"){
            steps{
                sh'npm publish '
            }
        }
        stage('Build Docker Image'){
            steps{
                sh'docker stop s4'
                sh 'docker rm s4'
                sh 'docker build -t venkatesh9691/venkatesh-projects-new .'
                sh 'docker build -t tomcat:${BUILD_NUMBER} .'
                sh 'docker run -itd --name s4 -p 3800:3000 tomcat:${BUILD_NUMBER}'
            }
        }
        stage("Docker image scaning"){
            steps{
                sh'trivy image --format table -o trivy-fs-report.html venkatesh9691/venkatesh-projects-new'
            }
        }
        stage("Pushing Docker image to HUB"){
            steps{
                withCredentials([string(credentialsId: 'DOCKER_HUB_CREDENTIALS', variable: 'DOCKER_HUB_CREDENTIALS')]) {
                    sh "docker login -u venkatesh9691 -p ${DOCKER_HUB_CREDENTIALS}"
                }
                sh 'docker push venkatesh9691/venkatesh-projects-new'
            }
        }
    }
}
