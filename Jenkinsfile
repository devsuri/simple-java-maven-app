#!groovy
pipeline {
    agent any
    /*parameters { 
	    choice(name: 'CHOICES', choices: ['one', 'two', 'three'], description: 'Deployment Portion') 
    }*/
    options {
    buildDiscarder(logRotator(numToKeepStr: '3'))
    }
    tools {
    maven 'M2_HOME'
    }
    stages {
        stage('Build') {
            steps {
		    // Run the maven build
                echo 'Clean Build'
		sh 'mvn clean compile'
            }
        }
	stage("Release scope") {
            steps {
                script {
                    
                    env.RELEASE_SCOPE = input message: 'User input required', ok: 'Release!',
                            parameters: [choice(name: 'RELEASE_SCOPE', choices: ['Shell','AWSCodeDeploy'], description: 'What is the release scope?')]
                //}
                //echo "Release scope selected: ${env.RELEASE_SCOPE}"
		    
			    if (env.RELEASE_SCOPE == "Shell")
		    {
			    echo "execute Shell"
			    //sshagent(['dev-server']) {
                    //sh "rsync -ivhr $WORKSPACE/ServiceInterface/bin/ -e 'ssh -o StrictHostKeyChecking=no' '${env.devsfws}':'/usr/share/nginx/www/DevRubyWS/bin/'"
                    //sh "ssh -o StrictHostKeyChecking=no '${env.devsfws}' 'sudo chmod +x /usr/share/nginx/www/DevRubyWS/bin'"
                //}
		    }
		    
		    
			    if (env.RELEASE_SCOPE == "AWSCodeDeploy")
		    {
			    echo "execute AWSCodeDeploy"
		    
		    }
		}
            }
        }
    
        
   stage('Test') {
            steps {
		    // Run integration test
                echo 'Testing'
                sh 'mvn test'
            }
        }
	    
	    
	    
	    
	
    }

    post {
        success {
            notifyBuild()
            
	}
	    
        
	 failure {
            notifyBuild('ERROR')
	  }
    }
}
// Slack notification with status and code changes from git
def notifyBuild(String buildStatus = 'SUCCESSFUL') {
    //buildStatus = buildStatus
    buildStatus =  buildStatus ?: 'SUCCESSFUL'

    def colorName = 'RED'
    def colorCode = '#FF0000'
    def subject = "${buildStatus}: Job Name - '${env.JOB_NAME}', Build No. - '${env.BUILD_NUMBER}', Git Commit - '${env.GIT_COMMIT}'"
    def changeSet = getChangeSet()
    def message = "${subject} \n ${changeSet}"

    if (buildStatus == 'SUCCESSFUL') {
        color = 'GREEN'
        colorCode = '#00FF00'
    } 
    else {
        color = 'RED'
        colorCode = '#FF0000'
    }

    slackSend(color: colorCode, message: message)
    //slackUploadFile filePath: '/var/jenkins_home/workspace/pipeline-jenkinstest/arachni_report.html.zip', initialComment:  'HEY HEY'
}

// Fetching change set from Git
@NonCPS
def getChangeSet() {
    return currentBuild.changeSets.collect { cs ->
    cs.collect { entry ->
            "* ${entry.author.fullName}: ${entry.msg}"
        }.join("\n")
    }.join("\n")
}
