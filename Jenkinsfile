#!groovy
import groovy.json.JsonSlurperClassic

  echo 'Starting the Process1...'

node {

	
    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY
    echo SF_CONSUMER_KEY
    
    def SF_USERNAME=env.SF_USERNAME
    echo SF_USERNAME
	
    def SERVER_KEY_CREDENTIALS_ID=env.SERVER_KEY_CREDENTIALS_ID
    echo SERVER_KEY_CREDENTIALS_ID
    def DEPLOYDIR='src'
    def TEST_LEVEL='RunLocalTests'
    def SF_INSTANCE_URL = 'https://test.salesforce.com'
    echo SF_INSTANCE_URL
    //def toolbelt = tool 'toolbelt'
    //echo toolbelt
    //
    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

   echo 'Starting the Process2...'
	
    stage('checkout source') {
        checkout scm
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------

 	withEnv(["HOME=${env.WORKSPACE}"]) {	
	
	    withCredentials([file(credentialsId: SERVER_KEY_CREDENTIALS_ID, variable: 'server_key_file')]) {
		// -------------------------------------------------------------------------
		// Authenticate to Salesforce using the server key.   ${toolbelt}
		// -------------------------------------------------------------------------

		stage('Authorize to Salesforce') {
			rc = command "sfdx auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --jwtkeyfile ${server_key_file} --username ${SF_USERNAME} --setalias UAT"
		    if (rc != 0) {
			error 'Salesforce org authorization failed.'
		    }
		}


		// -------------------------------------------------------------------------
		// Deploy metadata and execute unit tests. ${toolbelt}
		// -------------------------------------------------------------------------

		stage('Deploy and Run Tests') {
		    rc = command "sfdx force:mdapi:deploy --wait 10 --deploydir ${DEPLOYDIR} --targetusername UAT --testlevel ${TEST_LEVEL}"
		    if (rc != 0) {
			error 'Salesforce deploy and test run failed.'
		    }
		}


		// -------------------------------------------------------------------------
		// Example shows how to run a check-only deploy.
		// -------------------------------------------------------------------------

		//stage('Check Only Deploy') {
		//    rc = command "${toolbelt}/sfdx force:mdapi:deploy --checkonly --wait 10 --deploydir ${DEPLOYDIR} --targetusername UAT --testlevel ${TEST_LEVEL}"
		//    if (rc != 0) {
		//        error 'Salesforce deploy failed.'
		//    }
		//}
	    }
	}
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
		return bat(returnStatus: true, script: script);
    }
}
