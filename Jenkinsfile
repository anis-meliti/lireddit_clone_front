node {
    def nodeHome = tool name: 'node-14.15.1', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
    env.PATH = "${nodeHome}/bin:${env.PATH}"
        def d=new Date().format( 'yyyyMMdd' )
    

    stage('check tools') {
        sh "node -v"
        sh "npm -v"
    }

    stage('checkout') {
        checkout scm
    }

    stage('npm install') {
        sh "npm install"
    }
   
   stage ('run project'){
      sh "npm run build"
   }
   
   stage('Create artifact after build') {

        sh "cd .next && ls"
        sh "tar czvf next.tar.gz .next"
        sh "tar czvf app.{$d}.tar.gz .next"
        archiveArtifacts "**/*.tar.gz"
    }
       stage('Running SonarQube Scanner') {
       steps {
        script {
          // requires SonarQube Scanner 2.8+
          scannerHome = tool 'SonarScanner'
        }
        withSonarQubeEnv('Tonis SonarQube') { // If you have configured more than one global server connection, you can specify its name
          sh "${scannerHome}/bin/sonar-scanner"
        }
       }
    }
   
    // No need to occupy a node
    stage("Quality Gate"){
     steps {
         script {
            timeout(time: 2, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
              def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
              if (qg.status != 'OK') {
                error "Pipeline aborted due to quality gate failure: ${qg.status}"
              }
            }
         }
      }

}