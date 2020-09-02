properties([[$class: 'JiraProjectProperty'], parameters([choice(choices: 'master\nrelease\nbugfix', description: 'select the branch to build', name: 'branch')])]) && 
properties([[$class: 'JiraProjectProperty'], parameters([choice(choices: 'QA1\nQA2\nDEV\nUAT\nPROD', description: 'choose any ENV', name: 'ENV'), string(defaultValue: 'WIN', description: 'choose any platform', name: 'Platform', trim: false)])])

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
				git credentialsId: 'git_Credentials', url: 'https://github.com/harikrishnapalakila/Reliance_CICD.git'
			
			}
			
		}
	
	
	 	stage('Maven Build - Clean'){
			steps{
			bat "mvn clean"
			
			}
 	}
	stage('Maven Build - compile'){
			steps{
			bat "mvn compile"
			
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
			       credentialsId: 'Nexus3_21_Credentials', 
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
		
		
		stage('Email Notification') {
			steps {
		mail bcc: '', body: '''Hi Team,
		Jenkins Build Job has been executed successfully''', cc: '', from: '', replyTo: '', subject: 'jenkins job', to: 'harikrishnapalakila@gmail.com'
			}
		}
		
		
		
		//stage('Copy file from stage1 to stage2') {
		//	parallel{ 
		//		stage('Test on windows'){agent {label 'windows'} "steps"{ bat "copy D:\\Krishna\\AWS\\jenkins_declarative_pipeline\\parallel_branch_code\\stage1\\parallel_branch_scripted.txt ..\\stage2\\"}  }	
		//		stage('Test on windows2'){agent {label 'windows'}"steps"{ bat "rename D:\\Krishna\\AWS\\jenkins_declarative_pipeline\\parallel_branch_code\\stage1\\stage2.txt stage2_changedfile.txt"} }						     
		// }
		//			       }
		
		
		
		
		
		
		
		
		
 	}
	post {
		always {
			echo "Hello ------->>>>>  !!!!!@@@@@@@@############{   Harikrishna  Palakila }#######@@@@@@@@!!!!!!! Pipeline Build finished Successfully"
			echo "Hello ------->>>>>  !!!!!@@@@@@@@############{   Harikrishna  Palakila }#######@@@@@@@@!!!!!!! Pipeline Build finished Successfully"
			
		}
	}		

			
}
 
