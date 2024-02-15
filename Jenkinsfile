pipeline {
  agent {
    docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
  }
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        //git branch: 'main', url: 'https://github.com/Charan5r/devops-automation.git'
      }
    }
    stage('Build and Test') {
      steps {
        sh 'ls -ltr'
        // build the project and create a JAR file
        sh 'mvn clean package'
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
            sh 'docker build -t charanrcs/devops-integration:${BUILD_NUMBER} .'
        }
      }
    }
    stage('Push image to Docker Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                   sh 'docker login -u charanrcs -p ARuna!@118'

}
                   sh 'docker push charanrcs/devops-integration:${BUILD_NUMBER}'
                }
            }
        }
    stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "devops-automation"
            GIT_USER_NAME = "charan5r"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "charansairatham@gmail.com"
                    git config user.name "Charan R"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    P_BUILD=$(($BUILD_NUMBER+1))
                    sed -i "s/35/${BUILD_NUMBER}/g" deploymentservice.yaml 
                    git add deploymentservice.yaml
                    
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
            }
        }
    }
  }
}
