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
          git clone https://github.com/cbci-pipeline/controller.git controller
          mkdir -p bundles/cbci-pipeline-controller
          cd controller/bundle
          cp --parents `find -name \\*.yaml*` ../../bundles/cbci-pipeline-controller/
          cd ../../bundles/
          zip -r bundle.zip .
          cd controller/bundle/items
          yq v pipelines.yaml
        '''
        withCredentials([usernamePassword(credentialsId: 'admin-cli-token', usernameVariable: 'JENKINS_CLI_USR', passwordVariable: 'JENKINS_CLI_PSW')]) {
          sh  '''
            pwd
            mkdir unzip
            cd ./unzip
            unzip ../bundles/bundle.zip
            ls -la
            cd cbci-pipeline-controller
            ls -la
            cd items
            ls -la
            cd ../../..
            curl -i --user "$JENKINS_CLI_USR:$JENKINS_CLI_PSW" -XPOST \
              -H "Accept: application/json"  \
              -H "Content-type: application/zip;charset=utf-8" --data-binary "@./bundles/bundle.zip" \
              http://cjoc/cjoc/casc-bundle/pre-validate-bundle
          '''
        }
      }              
    } 
  }
}
