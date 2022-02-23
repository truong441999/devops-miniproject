pipeline {
  agent any
  environment{
    DOCKERHUB_CREDENTIALS = credentials('docker_cred')
    GITHUB_CREDENTIALS = credentials('github_cred')
    BRANCH_NAME = "${GIT_BRANCH.split("/")[0]}"
    PULL_REQUEST_ID = "${ghprbPullId}"
    TARGET_BRANCH = "${ghprbTargetBranch}"
    SOURCE_BRANCH = "${ghprbSourceBranch}"
  }
  
  stages{ 
    stage('Clone repository') {
        steps{
            git branch: SOURCE_BRANCH,
            credentialsId: 'github_cred',
            url: 'https://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@github.com/daniellee152/devops.git'
        }
    }
    
    stage("Unit Testing PROD ENV"){
      steps{    
        sh "git branch"
        sh "echo Unit PROD DEV"
      }
    }
    
    stage("Git Merge Pull Request PROD ENV"){
        steps{
            // sh 'git reset --hard HEAD'
            // sh 'git clean -ffd'
            // sh "git pull"
            // sh "git push origin HEAD:dev"
            
            sh "git branch"
            sh "git pull origin ${SOURCE_BRANCH}"
            
            sh "git merge origin/${TARGET_BRANCH}"
            sh "git checkout ${TARGET_BRANCH}"
            sh "git merge --no-ff ${SOURCE_BRANCH}"
            sh "git push origin ${TARGET_BRANCH}"
            sh "git branch"
        }
    }
    
    stage("Build Docker Image PROD ENV"){
        steps{
            // Build Image 
            sh "git branch"
            sh "docker build -f ./server/Dockerfile ./server -t letiendat152/devops-server:latest"
            sh "docker build -f ./server/Dockerfile ./server -t letiendat152/devops-server:${PULL_REQUEST_ID}"
            
            // Push Image to Docker Hub
            sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
            sh 'docker image push letiendat152/devops-server:latest'
            sh 'docker image push letiendat152/devops-server:${PULL_REQUEST_ID}'
            
        }
    }
    
    stage("Update Deployment Manifest PROD ENV"){
        steps{
            // Change DEV deployment manifest
            sh "git branch"
            sh "cat ./deployment_manifest/prod/server-deployment.yaml"
            sh '''sed -i "s+letiendat152/devops-server.*+letiendat152/devops-server:${PULL_REQUEST_ID}+g" ./deployment_manifest/prod/server-deployment.yaml'''
            sh "cat ./deployment_manifest/prod/server-deployment.yaml"
            sh "git add ."
            sh "git commit -m'Done by Jenkins on prod manifest:${PULL_REQUEST_ID}'"
            sh "git pull origin master"
            sh "git push origin master"
        }
    }
  }
}
