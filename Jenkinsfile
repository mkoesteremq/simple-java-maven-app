pipeline {
  parameters {
    choice(
      name: "Environment",
      choices: ['staging', 'prod'],
      description : "Release Scope for basic-auth. Deployment for dev needs to be done manually ",
    )
    choice(
      name: "Version",
      choices: ['Patch', 'Minor', 'Major'],
      description : "SemVer-String",
    )
  }
  environment {
    stack_prefix = "${params.Environment == 'prod' ? 'xdn' : 'xdn-staging'}"
    stack_environment = "${params.Environment == 'prod' ? 'prod' : 'stage'}"
    builds_to_keep = "${params.Environment == 'prod' ? '5' : '1'}"
  }
  options {
        buildDiscarder(logRotator(numToKeepStr: env.builds_to_keep))
        disableConcurrentBuilds()
  }
  agent {
    docker {
      image 'maven:3-alpine'
      args '-v /root/.m2:/root/.m2'
    }
  }
  stages {
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }
    stage("test") {
      steps {
        sh 'mvn test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }
    stage("deliver"){
      steps {
        sh './jenkins/scripts/deliver.sh'
      }
    }
  }
}
