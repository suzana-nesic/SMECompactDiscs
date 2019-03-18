pipeline {
  agent any
  tools {
    maven 'Maven3.5.4'
  }
  stages {
    stage ('build/test') {
      steps() {
        sh 'mvn clean package'
      }
    }
    stage ('deliver') {
      steps() {
        sh '''
          aws s3 cp target/SpringDataRestBoot-0.0.1-SNAPSHOT.jar s3://sme-artifact-bucket/SpringDataRestBoot-0.0.1-SNAPSHOT.jar --region us-west-2
        '''
      }
    }  
    stage ('deploy') {
      steps() {
        sh '''
          aws cloudformation create-stack --stack-name smestack --template-body file://sme.template --parameters ParameterKey=InstanceType,ParameterValue=t2.micro ParameterKey=KeyName,ParameterValue=SMEKey ParameterKey=DBUser,ParameterValue=sme ParameterKey=DBPass,ParameterValue=Pass.123 --capabilities CAPABILITY_NAMED_IAM --region us-west-2
          aws cloudformation wait stack-create-complete --stack-name smestack --region us-west-2
          aws cloudformation describe-stack-events --stack-name smestack --region us-west-2 
        '''
      }
    }    
  }
}
