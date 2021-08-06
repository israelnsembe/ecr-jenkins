/* pipeline {
    agent any 
    environment {
        //TODO # 1 --> once you sign up for Docker hub, use that user_id here
        registry = "ananthkannan/mypython-app-may20"
        //TODO #2 - update your credentials ID after creating credentials for connecting to Docker Hub
        registryCredential = 'fa32f95a-2d3e-4c7b-8f34-11bcc0191d70'
        dockerImage = ''
    }
    
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '.../master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/akannan1087/myPythonDockerRepo']]])       
            }
        }
    
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }
    
     // Uploading Docker images into Docker Hub
    stage('Upload Image') {
     steps{    
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            }
        }
      }
    }
    
     // Stopping Docker containers for cleaner Docker run
     stage('docker stop container') {
         steps {
            sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
         }
       }
    
    
    // Running Docker container, make sure port 8096 is opened in 
    stage('Docker Run') {
     steps{
         script {
            dockerImage.run("-p 8096:5000 --rm --name mypythonContainer")
         }
      }
    }
  }
} */

// Build by me with aws EC2 server jenk-jenk

pipeline {
    agent any

    environment {
        registry = "977237087701.dkr.ecr.us-east-1.amazonaws.com/izzy-ecr001"
    }

    stages {
        stage ('Chaeckout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/israelnsembe/ecr-jenkins.git']]])
            }
        }
        stage ('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage ('Docker Push') {
            steps{
                script {
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 977237087701.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker push 977237087701.dkr.ecr.us-east-1.amazonaws.com/izzy-ecr001:$BUILD_NUMBER'
                }
            }
        }
        stage ('Stop Previous Container') {
            steps {
                sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
                sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
            }
        }
        stage ('Docker Run') {
            steps {
                script {
                    sh 'docker run -d -p 8090:5000 --rm --name=mypythonContainer 977237087701.dkr.ecr.us-east-1.amazonaws.com/izzy-ecr001:$BUILD_NUMBER'
                }
            }
        }
    }
}
