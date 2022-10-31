pipeline {
  agent any                                                     // The parameters specified in the Jenkinsfile will appear in the job only after the first run. Our first job run will fail as we will not be able to provide the parameter value through the job.
   parameters {                                                 // Parameters --> build parameter allows us to pass data into our Jenkins jobs. Using build parameters, we can pass any data we want: git branch name, secret credentials etc. We can access a parameter at any stage of a pipeline. Accessing parameters in stages is pretty straightforward. We just have to use params.[NAME] in places where we need to substitute the parameter.
  choice choices: ['NODE', 'PYTHON'], name: 'Branch'            // choice --> it is parameter type and we have two choices NODE & PYTHON and the parameter name is Branch, and we access it in the stage as params.branch
}
    stages{
    stage ('Build') {
      steps {
        sh 'printenv'                                           // printenv is a shell command to print all the env variables we have defined.
      }
    }
    stage ('Publish to ECR') {
      steps {
          script {
          if (params.Branch == "NODE") {                       // based on the input provided by choice parameter (Node & Python) the respective methods will be invoked.
            DeployNodeApp()
        } else {
           DeployPythonApp ()
        }
          }
      }
   }
 }
}


def DeployNodeApp()                                          // We defined our methods DeployNodeApp() & DeployPythonApp() and the deployment is happening based on input parameters (Node & Python)
{
    git url: 'https://github.com/amigo-nishant/Simple-hello-world-nodeJS.git', branch: "main"
    try {
withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
          sh 'docker login -u AWS -p $(aws ecr get-login-password --region eu-west-2) 927491280662.dkr.ecr.eu-west-2.amazonaws.com' 
          sh 'docker build -t jenkins-pipeline-build .'
          sh 'docker tag jenkins-pipeline-build:latest 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build:latest'
          sh 'docker push 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build:latest'
          sh 'ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/dev-server.pem ec2-user@ec2-35-176-254-143.eu-west-2.compute.amazonaws.com aws ecr get-login-password --region eu-west-2 | docker login --username AWS --password-stdin 927491280662.dkr.ecr.eu-west-2.amazonaws.com'
          sh 'ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/dev-server.pem ec2-user@ec2-35-176-254-143.eu-west-2.compute.amazonaws.com docker run -d -p 8081:4000 927491280662.dkr.ecr.eu-west-2.amazonaws.com/jenkins-pipeline-build'
}
}
catch(e) {}                                                 // We have try and catch block for exception handling  
}
 
def DeployPythonApp ()
{
git url: 'https://github.com/amigo-nishant/Python-hello-world.git', branch: "main"
 try {
 withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
          sh 'docker login -u AWS -p $(aws ecr get-login-password --region eu-west-2) 927491280662.dkr.ecr.eu-west-2.amazonaws.com' 
          sh 'docker build -t python-pipeline .'
          sh 'docker tag python-pipeline:latest 927491280662.dkr.ecr.eu-west-2.amazonaws.com/python-pipeline:latest'
          sh 'docker push 927491280662.dkr.ecr.eu-west-2.amazonaws.com/python-pipeline:latest'
          sh 'ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/python-dev.pem ec2-user@ec2-13-41-226-172.eu-west-2.compute.amazonaws.com aws ecr get-login-password --region eu-west-2 | docker login --username AWS --password-stdin 927491280662.dkr.ecr.eu-west-2.amazonaws.com'
          sh 'ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/python-dev.pem ec2-user@ec2-13-41-226-172.eu-west-2.compute.amazonaws.com sudo docker run -d -p 5002:5000 927491280662.dkr.ecr.eu-west-2.amazonaws.com/python-pipeline:latest'
}
}
catch(e) {}
}

