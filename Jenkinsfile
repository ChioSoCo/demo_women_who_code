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
          sh "ls -lha"
          sh "docker ps -a"
          sh "tar -cvf angularapp.tar angular-todo-app/"
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
          sh "tar -xvf angularapp.tar"
          sh """
              docker run --name Angular -v '${WORKSPACE}'/angular-todo-app:/home/circleci/Angular -i myangular:v1.1 
              cd Angular
              npm install
              #ng version
              ng build
              exit
              ls -lha angular-todo-app/
              docker rm Angular
              docker ps -a
              """
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
