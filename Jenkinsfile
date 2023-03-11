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
          cd controller
          mkdir -p cbci-pipeline-controller
          cd bundle
          cp --parents `find -name \\*.yaml*` ../cbci-pipeline-controller/
          cd ..
          zip -r bundle.zip . -x .git\\* controller.yaml bundle\\*
        '''
        withCredentials([usernamePassword(credentialsId: 'admin-cli-token', usernameVariable: 'JENKINS_CLI_USR', passwordVariable: 'JENKINS_CLI_PSW')]) {
          sh  '''
            pwd
            mkdir unzip
            cd ./unzip
            unzip ../controller/bundle.zip
            ls -la
            cd cbci-pipeline-controller
            ls -la
            cd items
            ls -la
            cd ../../..
            curl -i --user "$JENKINS_CLI_USR:$JENKINS_CLI_PSW" -XPOST \
              -H "Accept: application/json"  \
              -H "Content-type: application/zip;charset=utf-8" --data-binary "@./controller/bundle.zip" \
              http://cjoc/cjoc/casc-bundle/pre-validate-bundle
          '''
        }
      }              
    } 
  }
}
