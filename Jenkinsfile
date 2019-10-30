pipeline {
  options {
    disableConcurrentBuilds()
  }
  agent {
    label "jenkins-maven"
  }
  environment {
    DEPLOY_NAMESPACE = "jx-staging"
  }
  stages {
    stage('Validate Environment') {
      steps {
        container('maven') {
          dir('env') {
            sh 'jx step helm build'
          }
        }
      }
    }
    stage('Update Environment') {
      when {
        branch 'master'
      }
      steps {
        container('maven') {
          dir('env') {
            sh 'jx step helm apply'
          }
        }
      }
    }
    stage ('Deploy FortiWeb') {
      when {
        branch 'master'
      }
      steps {
        container('maven') {
          dir('env') {
            sh "kubectl get pods -A"
            sh "kubectl apply -f fwb-deploy.yml"
          }
        }
      }
    }
  }
}
