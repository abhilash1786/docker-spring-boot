pipeline {
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "564552333382.dkr.ecr.ap-south-1.amazonaws.com/eks-repo"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/abhilash1786/docker-spring-boot']]])     
            }
        }
      stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
      }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry 
          dockerImage.tag("$BUILD_NUMBER")
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 564552333382.dkr.ecr.ap-south-1.amazonaws.com/eks-repo'
                sh 'docker push 564552333382.dkr.ecr.ap-south-1.amazonaws.com/eks-repo:$BUILD_NUMBER'
         }
        }
      }
        stage ('Helm Deploy') {
          steps {
            script {
                sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}
