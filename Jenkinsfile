pipeline {
  agent any
  stages {
    stage ('Build') {
      steps {
        sh 'printenv'
      }
    }
    stage ('Publish to ECR') {
      steps {
         withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
          sh 'docker login -u AWS -p $(aws ecr get-login-password --region eu-west-2) 927491280662.dkr.ecr.eu-west-2.amazonaws.com' 
          sh 'docker build -t jenkins-pipeline-build .'
          sh 'docker tag jenkins-pipeline-build:latest 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build:latest'
          sh 'docker push 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build:latest'
         }
       }
    }
    stage ('Deploy to EC2') {
      steps{
            sh "ssh -i "dev-server.pem" ec2-user@ec2-18-169-10-113.eu-west-2.compute.amazonaws.com"
            //scp -i "jenkins.pem" -r <l> ec2-user@ec2-35-178-44-200.eu-west-2.compute.amazonaws.com:<server_file_path>
            //sh 'aws ecr get-login-password --region eu-west-2 | docker login --username AWS --password-stdin 927491280662.dkr.ecr.eu-west-2.amazonaws.com'
            //sh 'docker pull 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build:latest'
            //sh 'docker run -d --name NodeJS-container -p 8080:4000 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build'
            //echo "success login"
         }
       }
    }
}
