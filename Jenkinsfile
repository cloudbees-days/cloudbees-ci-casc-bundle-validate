pipeline {
  agent { label 'default' }
  options { timeout(time: 4, unit: 'MINUTES') }
  triggers {
    eventTrigger jmespathQuery("controller.action=='casc_bundle_update'")
  }
  stages {
    stage('Validate Bundle') {
      steps {
        sh '''
          git clone https://github.com/cloudbees-days/workshop-casc-bundles.git controller
          cd ./controller/base/
          zip -r bundle.zip ./*
        '''
        withCredentials([usernamePassword(credentialsId: 'admin-cli-token', usernameVariable: 'JENKINS_CLI_USR', passwordVariable: 'JENKINS_CLI_PSW')]) {
          sh  '''
            curl --user "$JENKINS_CLI_USR:$JENKINS_CLI_PSW" -XPOST \
              -H "Accept: application/json"  \
              -H "Content-type: application/zip" --data-binary "@./controller/base/bundle.zip" \
              http://cbci-previews-demo-controller.controllers.svc.cluster.local/cbci-previews-demo-controller/casc-bundle-mgnt/casc-bundle-validate
          '''
        }
      }              
    } 
  }
}
