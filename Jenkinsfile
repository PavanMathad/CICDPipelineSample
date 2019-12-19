pipeline {
  agent {
    label 'jenkins-k8s-slave' 
  }
  
environment{
PYTHON_PATH='${WORKSPACE}'
access_token = credentials('Github_access_token')
ENVIRONMENT='development'
MYSQL_CONNECTION_STRING=credentials('connection_string')
BUCKET_NAME='pecten-duplication-development'

}

stages {
  stage('Install pip dependencies') {
        steps {
          sh 'pip3 install --upgrade virtualenv'
          sh 'python3 -m venv ${WORKSPACE}'

        }
    }

  stage('run automatic code review') {
      steps {
        withCredentials([file(credentialsId: 'pecten-google-sa-credential', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
       
	sh 'echo "$GOOGLE_SA_CRENTIAL"'
        sh ". ${WORKSPACE}/bin/activate && python3 ${WORKSPACE}/automatic_code_review.py" }
      }
    }
  
   stage('run Unit Test') {
      steps {
        withCredentials([file(credentialsId: 'pecten-google-sa-credential', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
       
	sh 'echo "$GOOGLE_SA_CRENTIAL"'
        sh ". ${WORKSPACE}/bin/activate && python3 ${WORKSPACE}/unittest/ut_Sample_dev.py" }
      }
    }
  stage('Merge master') {
	steps {
		withCredentials([file(credentialsId: 'pecten-google-sa-credential', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]){
  		sh 'git fetch --all'
  		sh 'git checkout staging'
  		sh 'git checkout development'
		sh 'git merge staging'}
	      }
  }
	
 }
}
