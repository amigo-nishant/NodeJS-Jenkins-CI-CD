pipeline {
  agent any
    properties([parameters([choice(choices: 'Master\nDEV\nPROD', description: 'Select branch to Build', name: 'Branch')])])
  stage('SCM checkout'){
    echo "Pulling changes from the branch ${params.Branch}"
    git url: 'https://github.com/amigo-nishant/Simple-hello-world-nodeJS.git' branch: "${params.Branch}"
  }
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
              sh 'ssh  -o StrictHostKeyChecking=no -i /var/lib/jenkins/dev-server.pem ec2-user@ec2-18-169-10-113.eu-west-2.compute.amazonaws.com ls'
              sh 'ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/dev-server.pem ec2-user@ec2-18-169-10-113.eu-west-2.compute.amazonaws.com aws ecr get-login-password --region eu-west-2 | docker login --username AWS --password-stdin 927491280662.dkr.ecr.eu-west-2.amazonaws.com'
              sh 'ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/dev-server.pem ec2-user@ec2-18-169-10-113.eu-west-2.compute.amazonaws.com docker pull 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build:latest'
              sh 'ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/dev-server.pem ec2-user@ec2-18-169-10-113.eu-west-2.compute.amazonaws.com docker run -d -p 8081:4000 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build'
         }
       }
    }
 }

