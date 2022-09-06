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
          
          mkdir -p cbci-previews-demo-controller
          git clone https://github.com/cbci-previews-demo/controller.git controller
        '''
        dir('contorller/bundle') {
          sh "cp --parents `find -name \\*.yaml*` ../../cbci-previews-demo-controller/"
        }
        sh '''
          ls -la cbci-previews-demo-controller
          zip -r bundle.zip cbci-previews-demo-controller
        '''
        withCredentials([usernamePassword(credentialsId: 'admin-cli-token', usernameVariable: 'JENKINS_CLI_USR', passwordVariable: 'JENKINS_CLI_PSW')]) {
          sh  '''
            curl --user "$JENKINS_CLI_USR:$JENKINS_CLI_PSW" -XPOST \
              -H "Accept: application/json"  \
              --header "Content-type: application/zip" --data-binary "@./controller/bundle/bundle.zip" \
              http://cjoc/cjoc/casc-bundle-mgnt/casc-bundle-validate
          '''
        }
      }              
    } 
  }
}
