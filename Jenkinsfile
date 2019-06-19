pipeline {
  agent
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
        script {
            sh "ls -lha"
        }
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
