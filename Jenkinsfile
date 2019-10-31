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
          sh "kubectl get pods -A"
          sh "kubectl apply -f fwb-deploy.yml"
          sh "cluster_ip=$(kubectl get svc  -n jx-staging  -o=jsonpath='{.items[0].spec.clusterIP}')"
          sh "port=$(kubectl get svc  -n jx-staging  -o=jsonpath='{.items[0].spec.ports[0].port}')"
          sh "echo $cluster_ip $port"
        }
      }
    }
  }
}
