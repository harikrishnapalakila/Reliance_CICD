currentBuild.displayName = "Reliance_Harikrishna_#"+currentBuild.number
pipeline{
	agent any
	
	options {
		buildDiscarder logRotator(daysToKeepStr: '5', numToKeepStr: '7')
	}

	environment {
		PATH = "${PATH}:D:/Krishna/Binaries/apache-maven-3.6.0-bin/apache-maven-3.6.0/bin"
		
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
	
		stage('SonarQube- Code Analysis'){
			steps{
				withSonarQubeEnv ('sonarqube_server_details') {
					bat "mvn sonar:sonar"                             
				    }
			}
		}
		
		stage('Maven Build - PKG with Rename war file'){
			steps{
			bat "mvn package"
			
			
			}
 	}
		stage('Upload War to Nexus'){
			steps{
				script{
				 def mavenPom = readMavenPom file: 'pom.xml'
				 def nexusRepoName = mavenPom.version.endsWith("SNAPSHOT") ? "simpleapp-snapshot" : "simpleapp-release"
					nexusArtifactUploader artifacts: [
			       [
				       artifactId: 'MavenWebApp', 
				       classifier: '', 
				       file: "target/MavenWebApp-${mavenPom.version}.war", 
				       type: 'war'
			       ]
		       ], 
			       credentialsId: 'Nexus_admin', 
			       groupId: 'com.codebind', 
			       nexusUrl: '192.168.56.1:8081', 
			       nexusVersion: 'nexus3', 
			       protocol: 'http', 
			       repository: nexusRepoName, 
			       version: "${mavenPom.version}"
				
				
				}
                       
			}
		}
		
		stage('Deploy to tomcat8'){
			steps{
			 script{
				 def mavenPom = readMavenPom file: 'pom.xml'
				 version: "${mavenPom.version}"
			bat "copy target\\MavenWebApp-${mavenPom.version}.war D:\\Krishna\\AWS\\tomcat\\apache-tomcat-8.5.39\\webapps\\"
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
 
