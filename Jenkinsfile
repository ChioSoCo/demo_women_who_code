pipeline {
  environment {
    repoUrl='https://github.com/ChioSoCo/angular-todo-app.git'
  }
  agent any
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
          sh "git clone '${repoUrl}'"
          sh "docker exec -it
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
