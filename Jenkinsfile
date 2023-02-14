pipeline {
  agent any 
  environment {
    AWS_ACCESS_KEY_ID = credentials('awsAccessKeyId') 
    AWS_SECRET_ACCESS_KEY = credentials('awsSecretAccessKey') 
    AWS_DEFAULT_REGION = 'ap-northeast-2'}
  stages {
    stage('Clone') {
      steps {
        echo 'Clone'
        git credentialsId: 'token_for_github', url: "https://github.com/ericsong917/s3_pipe"
      }
    }
    stage('MakeBackup') {
        steps {
            echo 'Make Backup (Move previous file to backup folder)'
            dir('/var/lib/jenkins/workspace/s3') { 
                script{
                    sh 'aws s3 mv s3://eric-website-bucket1231/ s3://eric-website-bucket1231/back-up --recursive --exclude \\"\\" --exclude "backup/*" --exclude "./*" --include "index.html" --include "my.css" --include "my.js"' //백업파일 생성
                }
            }
        }
    }
    stage('Upload new files') {
        steps {
            echo 'Upload new files'
            dir('/var/lib/jenkins/workspace/s3') { 
                script{
                    sh 'aws s3 cp ./ s3://eric-website-bucket1231 --recursive --include "./*" --exclude ".git/*" --exclude "Jenkinsfile" ' //이전 파일 삭제
                }
            
            }
        }
    }
    stage('Purge Cloudfront cache'){
      steps{
        echo 'purge cloudfront cache'
        script{
          sh 'aws cloudfront create-invalidation --distribution-id E2TKCJR2BV15LJ` --paths "/*"'
        }
      }
    }
  }
}