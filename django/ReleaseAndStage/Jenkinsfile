#!/usr/bin/groovy
@Library('github.com/fabric8io/fabric8-pipeline-library@master')
def utils = new io.fabric8.Utils()
clientsNode{
  def envStage = utils.environmentNamespace('stage')
  def newVersion = ''

  checkout scm
  stage('Build Release')
  echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
  if (!fileExists ('Dockerfile')) {
    writeFile file: 'Dockerfile', text: 'FROM django:onbuild'
  }

  newVersion = performCanaryRelease {}

  def rc = getDeploymentResources {
    port = 8000
    label = 'django'
    icon = 'https://cdn.rawgit.com/fabric8io/fabric8/dc05040/website/src/images/logos/django.svg'
    version = newVersion
    imageName = clusterImageName
  }

  stage('Rollout to Stage')
  def envStage = environmentNamespace("stage")
  kubernetesApply(file: rc, environment: envStage)
}
