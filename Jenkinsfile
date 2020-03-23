currentBuild.displayName = "Reliance_Harikrishna_#"+currentBuild.number
pipeline{
	agent any
	
	environment {
		PATH = "${PATH}:D:/Krishna/Binaries/apache-maven-3.6.0-bin/apache-maven-3.6.0/bin"
		 // This can be nexus3 or nexus2
		NEXUS_VERSION = "nexus3"
		// This can be http or https
		NEXUS_PROTOCOL = "http"
		// Where your Nexus is running
		NEXUS_URL = "192.168.56.1:8081"
		// Repository where we will upload the artifact
		NEXUS_REPOSITORY = "repository-example"
		// Jenkins credential id to authenticate to Nexus OSS
		NEXUS_CREDENTIAL_ID = "Nexus_admin_admin123"
	}
	stages{
	
		stage('SCM - Checkout'){
			steps{
				git credentialsId: 'git_Credentials', url: 'https://github.com/harikrishna12334/Reliance_CICD.git'
			
			}
		}
	
	
	 	stage('Maven Build - Clean'){
			steps{
			bat "mvn clean"
			
			}
 	}
	
		
		stage('Maven Build - Test'){
			steps{
			bat "mvn test"
			
			}
 	}
	
		stage('Maven Build - PKG with Rename war file'){
			steps{
			bat "mvn package"
			bat "rename target\\MavenWebApp-0.0.1-SNAPSHOT.war MavenWebApp.war"
			
			}
 	}
		stage('Deploy to tomcat8'){
			steps{
			bat "copy target\\MavenWebApp.war D:\\Krishna\\AWS\\tomcat\\apache-tomcat-8.5.39\\webapps\\"
			
			}
 	}
		
		stage('SonarQube- Code Analysis'){
			steps{
				withSonarQubeEnv ('sonarqube_server_details') {
					bat "mvn sonar:sonar"                             
				    }
			}
		}
		
		
		stage("publish to nexus") {
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
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
		
		
		
		
		
		
		
		
		stage('Sonarqube analysis') {
			steps {
				script {
					scannerHome = tool 'sonar';
				}
				withSonarQubeEnv('SonarQube') {
					bat "D:\\Krishna\\AWS\\sonarqube\\sonar-scanner-cli-3.3.0.1492-windows\\sonar-scanner-3.3.0.1492-windows\\bin\\sonar-scanner.bat" 
				}
			}
		}	
		
		
		
				
		stage('Deploy to tomcat8-ec2-user'){
			steps{
			sshagent(['tomcat_ec2-user']) {
		     sh """
			scp -o StrictHostKeyChecking=no target\\MavenWebApp.war ec2-user@3.0.59.158:/opt/tomcat8/webapps/
		        ssh ec2-user@3.0.59.158 /opt/tomcat/bin/shutdown.sh
			ssh ec2-user@3.0.59.158 /opt/tomcat/bin/startup.sh
			"""
		  }
			
			
			}
		
			
			}
 	}
			
		
		
}
 
