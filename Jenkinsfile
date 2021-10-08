node('master'){

	stage('SCM Checkout'){
		git 'https://github.com/automateops123/welcometoskillrary-master.git'
	}
	
	
	stage('clean the workspace'){
		sh "/usr/share/apache-maven/bin/mvn clean"	
	}
	
	stage('Compile the code'){
		sh "/usr/share/apache-maven/bin/mvn compile"
	}
	
	stage('Source Code Analysis'){
		withSonarQubeEnv('sonarqube1'){
		sh "/usr/share/apache-maven/bin/mvn sonar:sonar -Dsonar.projectKey=jenkins-integration -Dsonar.host.url=http://175.41.183.57:9000 -Dsonar.login=e15564d90c10691e253a9441e48bbf31ba0e0343"
		}
	}
	
  	/*stage("Quality Gate"){
          	timeout(time: 1, unit: 'HOURS') {
              		def qg = waitForQualityGate()
              		if (qg.status != 'OK') {
                  	error "Pipeline aborted due to quality gate failure: ${qg.status}"
                }
            }
    	} */
	
	stage('Packaging the code'){
		sh "/usr/share/apache-maven/bin/mvn package"
	}	
	
	stage('Junit Testing Reports'){
		junit '**/target/surefire-reports/*.xml'
	}

	stage('jacoco reports'){
          jacoco(
                execPattern: '**/target/jacoco.exec',
                classPattern: '**/coverage/**',
                sourcePattern: '**/coverage/**',
                inclusionPattern: '**/*.class'
		)
   	}
	
	stage('Jfrog Artifactory Backup'){
		sh "/usr/share/apache-maven/bin/mvn deploy"
	}

	stage("slack channel"){
        	slackSend channel: '#automateops123', color: 'Blue', message: 'welcometoskillrary-master war file Successfully Deployed Jfrog Artifactory', teamDomain: 'automateopsworkspace', tokenCredentialId: 'slackintjenk'   
    }
	
	stage('Deploy approval'){
		input "Deploy to prod?"
	}
	
	stage('Deploy to QAServer'){
		deploy adapters: [tomcat8(credentialsId: 'Tomcat-Jenkins', path: '', url: 'http://13.229.83.141:9090')], contextPath: 'skillrary01', war: '**/*.war'
	}
	    
	stage("slack channel"){
        	slackSend channel: '#automateops123', color: 'Blue', message: 'Job is Successfully Deployed to QA server', teamDomain: 'automateopsworkspace', tokenCredentialId: 'slackintjenk'   
    }
}
