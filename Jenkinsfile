pipeline {
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
 stages {
      stage('checkout') {
           steps {
             
                git branch: 'main', url: 'https://github.com/opsgauraw/qesuite.git'
             
          }
        }
 }
}
