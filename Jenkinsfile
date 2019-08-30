pipeline {
  agent none
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
  }
  stages {
    stage('Web Tests') {
      agent {
        kubernetes {
          label 'nodejs-devoptics'
          yamlFile 'nodejs-pod.yaml'
        }
      }
      when {
        beforeAgent true
        branch 'development'
      }
      stages {
        stage('Nodejs Setup') {
          steps {
            checkout scm
            container('nodejs') {
              sh """
                npm i -S express pug
                node ./hello.js &
              """
            }
          }   
        }
        stage('Testcafe') {
          steps {
            container('testcafe') {
              sh '/opt/testcafe/docker/testcafe-docker.sh "chromium --no-sandbox" tests/*.js -r xunit:res.xml'
            }
          }   
        }
      }  
      post {
        success {
          stash name: 'app', includes: '*.js, public/**, views/*, Dockerfile'
        }
        always {
          junit 'res.xml'
        }
      } 
    }
    stage('Build and Push Image') {
      agent {
        kubernetes {
          label 'nodejs'
          yamlFile 'nodejs-pod.yaml'
        }
      }
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        checkout scm
        echo "TODO - build and push image"
      }
    }
  }
}
