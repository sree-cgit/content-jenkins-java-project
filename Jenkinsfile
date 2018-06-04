pipeline { 
    agent {
      label 'apache'
    }
    stages {
       stage('Unit Tests') {
           steps {
              sh 'ant -f test.xml'
              junit 'reports/result.xml'
           }
       }
       stage('build') {
          steps {
             sh 'ant -f build.xml -v'
            } 
        post {
           success {
             archive 'dist/*.jar'
           }
         }
        }
      stage('deploy') {
         steps {
             echo 'Deploying to Apache Web Server'
             sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
         }
     }
      stage('Testing on Centos') {
          agent {
          label 'centos'
          }
          steps {
              sh "wget http://sreeram23172.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
              sh 'pwd'
              sh 'hostname'
          }
      }
      stage('Promoting To Green'){
          agent {
             label 'apache'
          }
          steps {
             sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/"
          }   
      }
    }
}
