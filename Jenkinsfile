
currentBuild.displayName = "Reliance_Harikrishna_#"+currentBuild.number
pipeline{
	agent any
	
	options {
		buildDiscarder logRotator(daysToKeepStr: '2', numToKeepStr: '2')
	}

	environment {
		// PATH = "${PATH}:D:/Krishna/Binaries/apache-maven-3.6.0-bin/apache-maven-3.6.0/bin"
		PATH = "${PATH}:/usr/share/apache-maven/bin"
		
	}
		
	stages{
	
		stage('SCM - Checkout'){
			steps{
				git credentialsId: 'git_Credentials', url: 'https://github.com/harikrishnapalakila/Reliance_CICD.git'
			
			}
			
		}
	
	
	 	stage('Maven Build - Clean'){
			steps{
			sh 'mvn clean'
			
			}
 	}
	stage('Maven Build - compile'){
			steps{
			sh 'mvn compile'
			
			}
 	}
		
		stage('Maven Build - Test'){
			steps{
			sh "mvn compile"
			}
 	}
	
		stage('SonarQube- Code Analysis'){
			steps{
				withSonarQubeEnv ('sonar') {
					sh "mvn sonar:sonar"
				    }
			}
		}
		
		stage('Maven Build - PKG with Rename war file'){
			steps{
			sh "mvn package"
			
			
			}
 	}
		stage('Upload War to Nexus'){
			steps{
				script{
				 	nexusArtifactUploader artifacts: [
                                          [
						  artifactId: 'MavenWebApp', 
						  classifier: '', 
						  file: 'target/MavenWebApp-1.0.0.war', 
						  type: 'war'
					  ]
					], 
						credentialsId: 'nexus3', 
						groupId: 'com.codebind', 
						nexusUrl: '172.31.60.153', 
						nexusVersion: 'nexus3', 
						protocol: 'http', 
						repository: 'http://107.23.96.191:8081/repository/simpleapp-release', 
						version: '1.0.0'		
				
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
 
