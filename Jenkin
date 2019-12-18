pipeline {
  agent {
    label 'jenkins-k8s-slave' 
  }
  
  stage('Merge master') {
  sh 'git fetch --all'
  sh 'git checkout staging'
  sh 'git checkout development'
  sh 'git merge staging'
 }
}
