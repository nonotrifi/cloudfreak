pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'JDK8'
    }
    stages {      
        stage('Build maven') {
            steps { 
                sh 'pwd'      
                sh 'mvn clean install package'
            }
        }
        
        stage('Copy Artifact') {
            steps { 
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }
         
        stage('Build docker image') {
            steps {
                script {   
                    // Add Docker permissions check and service start commands
                    sh """
                    if ! groups $USER | grep -q '\\bdocker\\b'; then
                        echo 'User is not in the docker group. Adding user to docker group...'
                        sudo usermod -aG docker $USER
                        echo 'You need to log out and log back in for the changes to take effect.'
                        exit 1
                    else
                        echo 'User is already in the docker group.'
                    fi
                    
                    if ! sudo systemctl is-active --quiet docker; then
                        echo 'Docker service is not running. Starting Docker service...'
                        sudo systemctl start docker
                    else
                        echo 'Docker service is already running.'
                    fi
                    """      
                    def customImage = docker.build('initsixcloud/petclinic', "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push("${env.BUILD_NUMBER}")
                    }
                }
            }
        }
    }
}
