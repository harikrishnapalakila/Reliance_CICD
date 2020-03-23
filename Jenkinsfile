currentBuild.displayName = "Reliance_Harikrishna_#"+currentBuild.number
pipeline{
	agent any
	
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
		stage(‘SonarQube analysis’){
			// requires SonarQube Scanner 2.8+
			def scannerHome = tool ‘sonar’;
			withSonarQubeEnv(‘sonarqube_server_details’) {
				bat “${scannerHome}/bin/sonar-scanner”
			}
		}
		
		stage('SonarQube- Code Analysis'){
			steps{
				withSonarQubeEnv ('sonarqube_server_details') {
					bat "mvn sonar:sonar"                             
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
 
