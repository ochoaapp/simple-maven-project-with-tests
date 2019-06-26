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
      archiveArtifacts 'target/*.*'
   }
   
   stage('Publish to NEXUS4') {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;
                   
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
      
                        nexusPublisher nexusInstanceId: 'nexusrepo', nexusRepositoryId: 'maven-snapshots', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath:'**/target/surefire-reports/TEST-*.xml']], mavenCoordinate: [artifactId: 'simple-maven-project', groupId: 'test', packaging: 'jar', version: '1.0-SNAPSHOT']]]

                    }
                    else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
               
            }
   
   }
   
}
