node
{

   echo "git branch name: ${env.BRANCH_NAME}"
   echo "build number is: ${env.BUILD_NUMBER}"
   echo "node name is: ${env.NODE_NAME}"

   def mavenHome=tool name: "maven-3.9.11"
   try
    {

   stage('checkout')
   {
   notifyBuild('STARTED')
   git 'https://github.com/bhuvana136/maven-web-app.git'
   }
   stage('sonar')
   {
   sh "${mavenHome}/bin/mvn clean sonar:sonar"
   }
   stage('build')
   {
  sh "${mavenHome}/bin/mvn clean package"
   }
   stage('upload artifact')
   {
  sh "${mavenHome}/bin/mvn clean deploy"
   }
   stage('Deploy to Tomcat') {
        echo "Deploying WAR file using curl..."

        sh """
            curl -u bhuvana:bhuvana \
            --upload-file /var/lib/jenkins/workspace/bhuvana-pipeline-dev/target/maven-web-application.war \
            "http://34.227.60.189:9090//manager/text/deploy?path=/maven-web-application&update=true"
        """
    }
  
}  //   try ending

        catch (e) {
   
       currentBuild.result = "FAILED"

  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }

}   //  node ending

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: '#bhuvana-devops')
}
