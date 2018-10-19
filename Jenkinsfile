#!/usr/bin/groovy
pipeline {
  agent {
    kubernetes {
      label 'jenkins-k8s-cluster-svc'
      yamlFile 'jenkinsPodTemplate.yml'
    }
  }
  stages {
    stage('Checkout code') {
      steps {
        container('jnlp'){
          script{
            inputFile = readFile('Jenkinsfile.json')
            config = new groovy.json.JsonSlurperClassic().parseText(inputFile)
            containerTag = env.BRANCH_NAME + '-' + env.GIT_COMMIT.substring(0, 7)
            println "pipeline config ==> ${config}"
          } // script
        } // container('jnlp')
      } // steps
    } // stage
    stage('Initialise Helm ROW'){
      when {
          expression { config.buildBranch.contains(env.BRANCH_NAME) }
      }
      steps {
        container('gcloud-helm'){
          sh "helm repo add ${config.helm.repositoryName} ${config.helm.repositoryUrl}"
          sh "helm push ${config.helm.helmFolder}/ ${config.helm.repositoryName}"
        }//container gcloud-helm
      }//steps
    }//stage 
  } // stages
} // pipeline
