pipeline {
  agent {
    docker {
      image 'goforgold/build-container:latest'
    }
  }
  stages {
    stage('Build') {
      steps {
        sh 'npm install'
      }
    }
    stage('Create Packer AMI') {
        steps {
          withCredentials([
            usernamePassword(credentialsId: 'aws_cred', passwordVariable: 'AWS_SECRET', usernameVariable: 'AWS_KEY')
          ]) {
	    sh 'cd /root/packer'
	    sh 'pwd'
	    sh 'hostname -i'
            sh 'packer build -var aws_access_key=${AWS_KEY} -var aws_secret_key=${AWS_SECRET} packer.json'
        }
      }
    }
    stage('AWS Deployment') {
      steps {
          withCredentials([
            usernamePassword(credentialsId: 'aws_cred', passwordVariable: 'AWS_SECRET', usernameVariable: 'AWS_KEY'),
          ]) {
            sh 'rm -rf terraform_demo1'
            sh 'git clone https://github.com/manasb21/terraform_demo1.git'
            sh '''
               cd terraform_demo1
               terraform init
               terraform apply -auto-approve -var access_key=${AWS_KEY} -var secret_key=${AWS_SECRET}
            '''
        }
      }
    }
  }
  environment {
    npm_config_cache = 'npm-cache'
  }
}
