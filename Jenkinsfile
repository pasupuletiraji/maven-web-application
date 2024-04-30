node{
    def mavenHome = tool name:'maven3.9.6'
    try{
      stage('Checkout'){
        git credentialsId: 'caa72105-3dc0-4d58-88ff-49c5c9fe08c9', url: 'https://github.com/pasupuletiraji/maven-web-application.git'
    }
    stage('Build'){
        sh "${mavenHome}/bin/mvn clean package"
    }
    stage('ExecuteSonarQubeReport'){
        sh "${mavenHome}/bin/mvn clean sonar:sonar"
    }
    stage('UploadArtifactsIntoNexus'){
        sh "${mavenHome}/bin/mvn clean deploy"
    }
    stage('DeployApplintoTomcat'){
        sshagent(['0b2484b6-a080-4938-a4aa-951290996dd6']) {
            sh "scp  -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.9.91:/opt/apache-tomcat-9.0.87/webapps/ >/dev/null 2>&1"
}
    }
  
    }// tryclosing
    catch (e){
        currentBuild.result = "FAILED"
        throw e
    }// catch block closing
    finally {
        SendSlacknotification(currentBuild.result)
    }
}// nodeclosing

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
