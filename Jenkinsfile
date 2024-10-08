pipeline {
  agent { label 'jdk17' }
  options {
    disableConcurrentBuilds()
  }
  environment {}
  stages{
    stage('Preparation') {
      steps {
        echo "STARTED:\nJob '${env.JOB_NAME} [${env.BUILD_NUMBER}]'\n(${env.BUILD_URL})"
        checkout changelog: false, poll: false, scm: [$class: 'GitSCM', branches: [[name: "${env.GIT_COMMIT}"]], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout'], [$class: 'RelativeTargetDirectory', relativeTargetDir: "${buildSet}/${projectDir}"], [$class: 'CloneOption', depth: 0, noTags: false, reference: '', shallow: false], [$class: 'LocalBranch', localBranch: "${env.BRANCH_NAME}"]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins', url: "https://bitbucket.org/dsnyecm/${projectDir}.git"]]]
      }
    }
    stage('Build Docker') {
      steps {
        sh "docker build -t ${ARTIFACTORY_DOCKER_URL}/dsny/${project}:${tag}.${commitNum} ."
        }
    }
    stage('Push Docker') {
      steps {
          sh "docker login -u ${ARTIFACTORY_DOCKER_USER} -p ${ARTIFACTORY_DOCKER_PWD} ${ARTIFACTORY_DOCKER_URL}"
          sh "docker push ${ARTIFACTORY_DOCKER_URL}/dsny/${project}:${tag}.${commitNum}"
        }
    }
  }
  post {
    success {
      script {
        latestMessage = "\n---also tagged with 'latest'"
      }
      echo "SUCCESSFUL\nJob '${env.JOB_NAME} [${env.BUILD_NUMBER}]'\nDocker Image: '${tag}'${latestMessage}\n(${env.BUILD_URL})"
    }
    failure {
      echo "FAILED\nJob '${env.JOB_NAME} [${env.BUILD_NUMBER}]'\n(${env.BUILD_URL})"
    }
  }
}
