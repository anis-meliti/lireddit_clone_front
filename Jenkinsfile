pipeline {
    def nodeHome = tool name: 'node-14.15.1', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
    env.PATH = "${nodeHome}/bin:${env.PATH}"
  def d = new Date().format( 'yyyyMMdd' )

    stage('check tools') {
        sh 'node -v'
        sh 'npm -v'
    }

    stage('checkout') {
        checkout scm
    }

    stage('npm install') {
        sh 'npm install'
    }

  stage ('run project') {
      sh 'npm run build'
  }

  stage('Create artifact after build') {
        sh 'cd .next && ls'
        sh 'tar czvf next.tar.gz .next'
        sh "tar czvf app.{$d}.tar.gz .next"
        archiveArtifacts '**/*.tar.gz'
  }
  stage('Sonarqube') {
    environment {
        scannerHome = tool 'SonarQubeScanner'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
    }
  }
}
