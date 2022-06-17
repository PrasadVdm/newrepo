pipeline {
	agent any
	stages {
		stage('Fetching code'){
			steps{
				git branch: 'paac', url: 'https://github.com/PrasadVdm/vprofile-project.git'
			}
		}

		stage('Build'){
			steps{
				sh 'mvn install'
			}
		}
		stage ('Test') {
			steps{
				sh 'mvn test'
			}
		}
		stage ('Checkstyle Analysis') {
			steps {
				sh 'mvn checkstyle:checkstyle'
			}
		}
		stage ('Sonar Analysis') {
			environment {
				scannerHome = tool 'sonar4.7'
			}
			steps {
				withSonarQubeEnv('sonar') {
					sh '''${scannerHome}/bin/sonar-scanner  -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
				}
			}
		}
		stage ('Quality Gates') {
			steps {
				timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
			}
		}

	}
	stage('Upload artifact to Nexus') {
		steps {
			nexusArtifactUploader(
    			nexusVersion: 'nexus3',
    			protocol: 'http',
    			nexusUrl: '172.31.26.71:8081',
    			groupId: 'QA',
    			version: "V${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
    			repository: 'vprofile-repo',
    			credentialsId: 'nexuslogin',
    			artifacts: [
        			[artifactId: 'vproapp',
         			classifier: '',
         			file: 'target/vprofile-v2.war',
         			type: 'war']
    			]
 			)
		}
	}
  }

}
