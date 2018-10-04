#!/usr/bin/groovy

@Library('github.com/fabric8io/fabric8-pipeline-library@master')
def canaryVersion = "1.0.${env.BUILD_NUMBER}"
def utils = new io.fabric8.Utils()
def stashName = "buildpod.${env.JOB_NAME}.${env.BUILD_NUMBER}".replace('-', '_').replace('/', '_')
def envStage = utils.environmentNamespace('stage')
def envProd = utils.environmentNamespace('run')
def setupScript = null

mavenNode {
  checkout scm
  if (utils.isCI()) {

    mavenCI {
        integrationTestCmd =
         "mvn org.apache.maven.plugins:maven-failsafe-plugin:integration-test \
            org.apache.maven.plugins:maven-failsafe-plugin:verify \
            -Dnamespace.use.current=false -Dnamespace.use.existing=${utils.testNamespace()} \
            -Dit.test=*IT -DfailIfNoTests=false -DenableImageStreamDetection=true \
            -P openshift-it"
    }

  } else if (utils.isCD()) {
    /*
     * Try to load the script ".openshiftio/Jenkinsfile.setup.groovy".
     * If it exists it must contain two functions named "setupEnvironmentPre()"
     * and "setupEnvironmentPost()" which should contain code that does any extra
     * required setup in OpenShift specific for the booster. The Pre version will
     * be called _before_ the booster objects are created while the Post version
     * will be called afterwards.
     */
    try {
      setupScript = load "${pwd()}/.openshiftio/Jenkinsfile.setup.groovy"
    } catch (Exception ex) {
      echo "Jenkinsfile.setup.groovy not found"
    }

    echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
    container(name: 'maven', shell:'/bin/bash') {
      stage('Build Image') {
        mavenCanaryRelease {
          version = canaryVersion
        }
        //stash deployment manifests
        stash includes: '**/*.yml', name: stashName
      }
    }
  }
}

if (utils.isCD()) {
  node {
    stage('Rollout to Stage') {
      unstash stashName
      setupScript?.setupEnvironmentPre(envStage)
      apply {
        environment = envStage
      }
      setupScript?.setupEnvironmentPost(envStage)
    }
  }
}

