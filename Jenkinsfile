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
		    }
		    
		    
			    if (env.RELEASE_SCOPE == "AWSCodeDeploy")
		    {
			    echo "execute AWSCodeDeploy"
		    
		    }
		}
            }
        }
       /* stage("Gather Deployment Parameters") {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    script {
		
                        // Show the select input modal
                       def INPUT_PARAMS = input message: 'Please Provide Parameters', ok: 'Next',
                                        parameters: [
                                        choice(name: 'Deployment', choices: ['Shell','AWSCodeDeploy'].join('\n'), description: 'Please select the Environment')]
                                        //choice(name: 'IMAGE_TAG', choices: getDockerImages(), description: 'Available Docker Images')]
                        //env.ENVIRONMENT = INPUT_PARAMS.ENVIRONMENT
                        //env.IMAGE_TAG = INPUT_PARAMS.IMAGE_TAG
			    //sh "echo ${INPUT_PARAMS.Deployment}"
                    }
                }
            }
         }
         stage("Use Deployment Parameters") {
         steps {
	 sh "echo ${env.Deployment}"
		 //sh "echo ${params.CHOICES}"
                //script {
                //    echo "All parameters have been set as Environment Variables"
                //    echo "Selected Deployment: ${params.Deployment}"
                   // echo "Selected Tag: ${env.IMAGE_TAG}"
               // }
            }
        }*/
    
        
  /*      stage('Test') {
            steps {
		    // Run integration test
                echo 'Testing'
                sh 'mvn test'
            }
        }
	    
        stage('JaCoCo') {
            steps {
                echo 'Code Coverage'
                jacoco()
            }
        }
        
        stage('Sonar-Static-Scanning'){
	        steps {
		        // Run the sonar scan
                echo 'Sonar Scanner'
		        // sh "mvn sonar:sonar -Dsonar.host.url=http://198.198.10.46:9000 -Dsonar.login=XXXXXXXXX"
		        withSonarQubeEnv('SonarQube') {
                    sh " mvn verify sonar:sonar -Dintegration-tests.skip=true -Dmaven.test.failure.ignore=true"
	            }
            }
	    }
		
        // waiting for sonar results based into the configured web hook in Sonar server which push the status back to jenkins
	    stage('Sonar scan result check') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    retry(3) {
                        script {
                            def qg = waitForQualityGate()
                            if (qg.status != 'OK') {
                                error "Pipeline aborted due to quality gate failure: ${qg.status}"
                            }
                        }
                    }
                }
            }
        }
      
	    stage('Deploy-from-featurebranch') {
            when {
		        expression {
		        return env.GIT_BRANCH == "origin/feature"
                }   
            }
            steps {
                echo 'Deploying to QA Environment'
                    //sshagent(['dev-server']) {
                    //sh "rsync -ivhr $WORKSPACE/ServiceInterface/bin/ -e 'ssh -o StrictHostKeyChecking=no' '${env.devsfws}':'/usr/share/nginx/www/DevRubyWS/bin/'"
                    //sh "ssh -o StrictHostKeyChecking=no '${env.devsfws}' 'sudo chmod +x /usr/share/nginx/www/DevRubyWS/bin'"
                //}
		    }   
        }
	    
	    stage('Deploy-from-develop-branch') {
            when {
		        expression {
		        return env.GIT_BRANCH == "origin/develop"
                }   
            }
            steps {
                echo 'Deploying to Stag Environment'
                    //sshagent(['dev-server']) {
                    //sh "rsync -ivhr $WORKSPACE/ServiceInterface/bin/ -e 'ssh -o StrictHostKeyChecking=no' '${env.devsfws}':'/usr/share/nginx/www/DevRubyWS/bin/'"
                    //sh "ssh -o StrictHostKeyChecking=no '${env.devsfws}' 'sudo chmod +x /usr/share/nginx/www/DevRubyWS/bin'"
                //}
		    }   
        }
	    
	    stage('Deploy-from-master-branch') {
            when {
		        expression {
		        return env.GIT_BRANCH == "origin/master"
                }   
             }
            steps {
                echo 'Deploying to Prod Environment'
                    //sshagent(['dev-server']) {
                    //sh "rsync -ivhr $WORKSPACE/ServiceInterface/bin/ -e 'ssh -o StrictHostKeyChecking=no' '${env.devsfws}':'/usr/share/nginx/www/DevRubyWS/bin/'"
                    //sh "ssh -o StrictHostKeyChecking=no '${env.devsfws}' 'sudo chmod +x /usr/share/nginx/www/DevRubyWS/bin'"
                //}
		    }   
        }
	    
	stage('Development deploy approval and deployment') {
            steps {
                script {
                    if (currentBuild.result == null || currentBuild.result == 'SUCCESS') {
                        timeout(time: 3, unit: 'MINUTES') {
                            // you can use the commented line if u have specific user group who CAN ONLY approve
                            //input message:'Approve deployment?', submitter: 'it-ops'
                            input message: 'Approve deployment?'
                        }
                        timeout(time: 2, unit: 'MINUTES') {
		            echo 'the application is deploying'
                        }
                    }
                }
            }
        }
        stage('Arachni-Dynamic-Scanning') {
	    steps {
		  echo 'Arachni-Dynamic-Scanning'
		  //sh 'mkdir $WORKSPACE/arachni_report'
                  sh 'rm -rf /var/jenkins_home/workspace/arachni_report/*'
                  sh 'rm -rf $WORKSPACE/arachni_report/*'
                  //sh '/arachni-1.4-0.5.10/bin/arachni "${env.domainname}" --report-save-path=/var/jenkins_home/workspace/arachni_report/arachni_report.afr'
		    sh '/arachni-1.4-0.5.10/bin/arachni http://35.171.80.62:8080/ --report-save-path=/var/jenkins_home/workspace/arachni_report/arachni_report.afr'
	          sh '/arachni-1.4-0.5.10/bin/arachni_reporter /var/jenkins_home/workspace/arachni_report/arachni_report.afr --reporter=html:outfile=/var/jenkins_home/workspace/arachni_report/arachni_report.html.zip'
                  sh 'cp /var/jenkins_home/workspace/arachni_report/arachni_report.html.zip $WORKSPACE/arachni_report'
	          sh 'chmod 777 -R $WORKSPACE/arachni_report'
                  //sh 'unzip /var/jenkins_home/workspace/pipeline-jenkinstest/arachni_report/arachni_report.html.zip -d arachni_report'
		  sh 'unzip $WORKSPACE/arachni_report/arachni_report.html.zip -d $WORKSPACE/arachni_report'
            }
        }
	stage('Publish HTML report') {
	    steps {
		  echo 'Arachni-Dynamic-Scanning Report'
	          publishHTML target: [
                  allowMissing: false,
                  alwaysLinkToLastBuild: false,
                  keepAll: true,
                  reportDir: 'arachni_report',
                  reportFiles: 'index.html',
                  reportName: 'Arachni-Dynamic-Scanning Report'
                  ]
	    }
	 }
	    
	stage('Blaze-Meter') {
            steps {
                echo 'Blaze-Meter'
                blazeMeterTest credentialsId: 'blazemeter-api-key', testId: '7498756.functionalGui', workspaceId: '400136'
	        
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
