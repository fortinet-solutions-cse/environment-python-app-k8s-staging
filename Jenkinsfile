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
          sh """
          cluster_ip=\$(kubectl get svc  -n jx-staging  -o=jsonpath='{.items[0].spec.clusterIP}')
          port=\$(kubectl get svc  -n jx-staging  -o=jsonpath='{.items[0].spec.ports[0].port}')
          fwb_ip=\$(kubectl get pods -l "app=fwb" -o=jsonpath={.items[0].status.podIP})
          echo \$cluster_ip \$port \$fwb_ip
          sed -i "s/{{cluster-ip}}/\${cluster_ip}/g" fwb-config.cfg 
          sed -i "s/{{cluster-port}}/\${port}/g" fwb-config.cfg
          cat fwb-config.cfg
          ssh admin@\$fwb_ip < fwb-config.cfg
          """
        }
      }
    }
  }
}
