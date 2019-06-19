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
        deleteDir()
        script {
          sh "git clone '${repoUrl}'"
          sh "tar -cf angularapp.tar angular-todo-app/"
        }
        stash includes: 'angularapp.tar', name: 'app' 
        }
        post {
        unstable {
          echo 'Execution failed'
        }//unstable
      }//post
    }//stage
    
    stage('Build App') {
      options { skipDefaultCheckout() }
      when {
      	anyOf { branch 'feature/*' }
      }
      steps {
        unstash 'app'
        script {
          sh "ls -lha"
          sh "tar -xf angularapp.tar"
          sh "ls -lha '${WORKSPACE}'/angular-todo-app"
          sh "ls -lha angular-todo-app"
          sh "docker run --name Angular -v '${WORKSPACE}'/angu:/Angular --rm myangular:v2.1 sh -c 'cd /Angular; pwd; ls -lha'"
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
