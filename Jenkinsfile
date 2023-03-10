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
          git clone https://github.com/cloudbees-days/workshop-casc-bundles.git bundles
          cd ./bundles
          zip -r bundles.zip ./*
        '''
        withCredentials([usernamePassword(credentialsId: 'admin-cli-token', usernameVariable: 'JENKINS_CLI_USR', passwordVariable: 'JENKINS_CLI_PSW')]) {
          sh  '''
            pwd
            mkdir unzip
            cd ./unzip
            unzip ../bundles/bundles.zip
            ls -la
            cd ops
            ls -la
            cd items
            ls -la
            cd ../../..
            curl -i --user "$JENKINS_CLI_USR:$JENKINS_CLI_PSW" -XPOST \
              -H "Accept: application/json"  \
              -H "Content-type: application/zip;charset=utf-8" --data-binary "@./bundles/bundles.zip" \
              http://cjoc/cjoc/casc-bundle/pre-validate-bundle
          '''
        }
      }              
    } 
  }
}
