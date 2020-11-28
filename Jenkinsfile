node {
    def nodeHome = tool name: 'node-9.8.0', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
    env.PATH = "${nodeHome}/bin:${env.PATH}"

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
         d=`date +%m-%d-%Y`
        echo $d
        sh "tar czvf next.tar.gz .next"
        sh "tar czvf app.$d.tar.gz .next"
        archiveArtifacts "**/*.tar.gz"
    }
}