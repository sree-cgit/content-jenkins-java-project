pipeline { 
    agent {
      label 'apache'
    }
    environment {
       MAJOR_VERSION = 1
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
             archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
           }
         }
        }
      stage('deploy') {
         steps {
             echo 'Deploying to Apache Web Server'
             sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
             sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}"
         }
     }
      stage('Testing on Centos') {
          agent {
          label 'centos'
          }
          steps {
              sh "wget http://sreeram23172.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
              sh 'pwd'
              sh 'hostname'
          }
      }
      stage('Promoting To Green'){
          agent {
             label 'apache'
          }
          when {
             branch 'master'
          }
          steps {
             sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/"
          }   
      }
      stage('Promote Development Branch to Master') {
          agent {
            label 'apache'
          }
          when {
            branch 'development'
          }
          steps {
            echo "Stashing Any Local Changes"
            sh 'git stash'
            echo "Checking Out Development Branch"
            sh 'git checkout development'
            echo 'Checking Out Master Branch'
            sh 'git pull origin'
            sh 'git checkout master'
            echo 'Merging Development into Master Branch'
            sh 'git merge development'
            sh 'git remote set-url origin https://sree-cgit:Sree.17%40infy@github.com/sree-cgit/content-jenkins-java-project.git'
            echo 'Pushing to Origin Master'
            sh 'git push origin master'
            echo "Tagging"
            sh "git tag rectangles_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
            sh "git push origin rectangles_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}" 
          }
      } 
    }
}
