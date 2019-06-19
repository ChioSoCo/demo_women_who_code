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
    }//stage
    
    stage('Build App') {
      options { skipDefaultCheckout() }
      when {
      	anyOf { branch 'feature/*' }
      }
      steps {
        unstash 'app'
        script {
          sh """
          tar -xf angularapp.tar
          docker run -d --name Angular teracy/angular-cli sleep infinity
          docker cp '${WORKSPACE}'/angular-todo-app Angular:/tmp
          docker exec -i Angular sh -c "cd /tmp/angular-todo-app; ls -lha; npm install; ng build; ls -lha"
          docker exec -i Angular sh -c "cd /tmp/angular-todo-app; tar -cf builded_app.tar dist/"
          docker cp Angular:/tmp/angular-todo-app/builded_app.tar .
          docker stop Angular
          docker rm Angular
          """
        }
        stash includes: 'builded_app.tar', name: 'buildedapp' 
      }
    }//stage
    
    stage('Deploy A') {
      options { skipDefaultCheckout() }
      when {
      	anyOf { branch 'feature/*' }
      }
      steps {
        unstash 'buildedapp'
        script {
          sh "ls -lha"
          sh """
          docker cp builded_app.tar Nginx:/usr/share/nginx/html
          docker exec -i Nginx sh -c "cd /usr/share/nginx/html; tar -xf builded_app.tar; mv dist/index.hmtl ." 
          docker restart Nginx
          """
        }
      }
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
