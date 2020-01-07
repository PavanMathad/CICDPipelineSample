def didAutoCodeReviewSucceed = True
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
		
	script {
                   didAutoCodeReviewSucceed = sh(script: ". ${WORKSPACE}/bin/activate && python3 ${WORKSPACE}/automatic_code_review.py", returnStdout: true)
		   //sh 'python3 ${WORKSPACE}/automatic_code_review.py; echo $? > status'
		   //didAutoCodeReviewSucceed = readFile('status').trim()
	           //didAutoCodeReviewSucceed = sh(script: 'python3 ${WORKSPACE}/automatic_code_review.py', returnStdout: true).split("\r?\n")
		   //didAutoCodeReviewSucceed = bat(returnStdout: true, script: 'python3 ${WORKSPACE}/automatic_code_review.py')
		   if(didAutoCodeReviewSucceed)
	      		currentBuild.result = 'UNSTABLE'
               }
		
	sh 'echo "$didAutoCodeReviewSucceed"'
	if(didAutoCodeReviewSucceed == True)
	      currentBuild.result = 'UNSTABLE'
	}
	
      }
    }
  
   stage('run Unit Test') {
      steps {
	      script {
	      if(didAutoCodeReviewSucceed)
	      {
        	withCredentials([file(credentialsId: 'pecten-google-sa-credential', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
       
		sh 'echo "$GOOGLE_SA_CRENTIAL"'
        	sh ". ${WORKSPACE}/bin/activate && python3 ${WORKSPACE}/unittest/ut_Sample_dev.py" }
	      }
	    }
      }
    }
  /*stage('Merge master') {
	steps {
		withCredentials([file(credentialsId: 'pecten-google-sa-credential', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]){
		sh 'git url: "ssh://jenkins@https://github.com/PavanMathad/CICDPipelineSample.git",credentialsId: 'pecten-google-sa-credential',branch:development'
		sh 'git config --global user.email "pavan@pecten.co.uk"'
  		sh 'git config --global user.name "PavanMathad"'
  		sh 'git tag -a tagName -m "Your tag comment"'
		sh 'git merge development'
		sh 'git commit -am "Merged develop branch to master'
		sh "git push origin master"}
	      }
  }
*/	
 }
}
