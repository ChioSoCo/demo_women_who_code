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
    
    stage('Deploy B') {
      options { skipDefaultCheckout() }
      when {
      	anyOf { branch 'feature/*' }
      }
      steps {
        unstash 'buildedapp'
        script {
          timeout(time: 5, unit: 'MINUTES') {
            // Show the select input modal
            env.INPUT_PARAMS = input message: 'Deploy to region B', ok: 'Deploy',
         		env.TYPE_TEST = env.INPUT_PARAMS
          }//timeout
          
          sh "ls -lha"
          sh """
          docker cp builded_app.tar Nginx2:/usr/share/nginx/html
          docker exec -i Nginx2 sh -c "cd /usr/share/nginx/html; tar -xf builded_app.tar; mv dist/ngx-behance/* ." 
          docker restart Nginx2
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
