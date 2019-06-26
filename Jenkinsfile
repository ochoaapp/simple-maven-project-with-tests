node {
   def mvnHome
   stage('Get Build Files') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/ochoaapp/simple-maven-project-with-tests.git'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'M3'
   }
   stage('Build') {
      // Run the maven build
      withEnv(["MVN_HOME=$mvnHome"]) {
         if (isUnix()) {
            sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
         } else {
            bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
         }
      }
   }
   stage('Test Results') {
      junit '**/target/surefire-reports/TEST-*.xml'
      archiveArtifacts 'target/*.jar'
   }
   
   stage('Publish to NEXUS') {
      nexusPublisher nexusInstanceId: 'nexusrepo', nexusRepositoryId: 'maven-snapshots', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'simple-maven-project-with-tests-v2-1.0-SNAPSHOT.jar']], mavenCoordinate: [artifactId: 'simple-maven-project-with-tests-v2', groupId: 'test', packaging: 'jar', version: '1.0-SNAPSHOT']]]
   }
   
}
