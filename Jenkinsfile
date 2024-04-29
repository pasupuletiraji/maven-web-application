node{
    echo "The build number is: ${env.BUILD_NUMBER}"
    echo "The Job name is: ${env.JOB_NAME}"
    echo "The node name is: ${env.NODE_NAME}"
    echo "The build url is: ${env.BUILD_URL}"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
    def mavenHome = tool name:'maven3.9.6'
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
}
