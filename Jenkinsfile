pipeline {
  //environment {
    //repoUrl='https://github.com/ChioSoCo/ngx-behance.git'
  //}
  agent any
  options {
    timeout(time: 1, unit: 'HOURS')
  }

  stages {
    stage('SCM') {
      //options { skipDefaultCheckout() }
      when {
      	anyOf { branch 'feature/*' }
      }
      steps {
        //deleteDir()
        script {
          //sh "git clone '${repoUrl}'"
          sh "tar -cf angularapp.tar ngx-behance/"
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
          docker cp '${WORKSPACE}'/ngx-behance Angular:/tmp
          docker exec -i Angular sh -c "cd /tmp/ngx-behance; ls -lha; npm install; ng build; ls -lha"
          docker exec -i Angular sh -c "cd /tmp/ngx-behance; tar -cf builded_app.tar dist/"
          docker cp Angular:/tmp/ngx-behance/builded_app.tar .
          docker rm -f Angular
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
          docker exec -i Nginx sh -c "cd /usr/share/nginx/html; tar -xf builded_app.tar; mv dist/ngx-behance/* ." 
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
