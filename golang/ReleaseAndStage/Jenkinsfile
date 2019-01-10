@Library('github.com/fabric8io/osio-pipeline@master') _

osio {

  config runtime: 'go'

  ci {

    def resources = processTemplate(params: [
          release_version: "1.0.${env.BUILD_NUMBER}"
    ])

    build resources: resources

  }

  cd {

    def resources = processTemplate(params: [
          release_version: "1.0.${env.BUILD_NUMBER}"
    ])

    build resources: resources

    deploy resources: resources, env: 'stage'

  }
}
