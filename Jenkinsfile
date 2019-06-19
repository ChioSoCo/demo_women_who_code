pipeline {
  agent none
  environment {
    repoUrl='https://.git',
    credentialsid='1234-5678'
	}
  options {
    timeout(time: 1, unit: 'HOURS')
  }

  stages {
    stage('SCM') {
      options { skipDefaultCheckout() }
      when {
      	anyOf { branch 'feature/*' }
      }
      steps {
        deleteDir()
        checkout([
          $class: 'GitSCM', branches: [[name: '*/'+'master']],
          doGenerateSubmoduleConfigurations: false,
          extensions: [[$class: 'RelativeTargetDirectory'],
          [$class: 'MessageExclusion', excludeMessage: '(?s).*JENKINS_IGNORE.*']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: "${credentialsid}", url: "${repoUrl}"]]])
        script {
            sh "ls -lha"
        }
        archiveArtifacts artifacts: 'filename_to_store.txt'
        }
        post {
        unstable {
          echo 'Execution failed'
        }//unstable
      }//post
    }//stage

  }//stages

    post {
       always {
        script {
          sh "echo 'The execution'"
         }//script
       }
  }
}