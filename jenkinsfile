def label = "mypod-${UUID.randomUUID().toString()}"

podTemplate(label: label, containers: [
	containerTemplate(name: 'chrome', image: 'openjdk:8-jdk', privileged: true, command: '/bin/bash', ttyEnabled: true),
	])

 {

node(label) {
   // for display purposes
    // Clean workspace
    //deleteDir()

    // Get some code from a GitHub repository
    container('chrome'){
        stage('Preparation') {
      git credentialsId: 'ether', url: 'https://sunil.hari@innersource.accenture.com/scm/ethan/ethernew.git'    
      sh "apt-get update && apt-get -y install sudo"
      sh "pwd"
      sh "wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -"
      sh "echo 'deb http://dl.google.com/linux/chrome/deb/ stable main' >> /etc/apt/sources.list.d/google.list"
      sh "apt-get update && apt-get install -y google-chrome-stable"
      sh "curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -"
      sh "apt-get update && apt-get install -y nodejs && apt-get install -y npm"
      sh "npm install"
      sh "node -v"
      }

  stage('Unit Test') {
    isTestFailed=false;
    try {
      if (isUnix()) {
        sh "node_modules/.bin/truffle test test/*.sol"
        //sleep(10)
         // sh "cd node_modules/.bin/"
         //sh "truffle test --network=ganache"
        //sh "truffle test test/*.sol"
        } else {
        //bat(/start node_modules\\.bin\\ganache-cli/)
        //sleep(10);
        bat(/node_modules\.bin\truffle test test\*.sol/)
      }
    } catch (err) {
      isTestFailed = true;
    }
    // finally
     //{
     //  sh "sleep 1800"
    // }

    //Collect Unit test reports to show in Jenkins
    junit 'junit-results.xml'
    if(isTestFailed){
      error "Unit Tests Failed. Fix them immediately."
    }
  }

  stage('Integration Test') {
    isTestFailed=false;
    try {
      if (isUnix()) {
        //sh "node_modules\\.bin\\ganache-cli &"
        //sleep(10)
        sh "node_modules/.bin/truffle test test/*.js"
      } else {
        //bat(/start node_modules\\.bin\\ganache-cli/)
        //sleep(10);
        bat(/"truffle test test\\*.js/)
      }
    } catch (err) {
      isTestFailed = true;
    }

    //Collect Unit test reports to show in Jenkins
    junit 'junit-results.xml'
    if(isTestFailed){
      error "Integration Tests Failed. Fix them immediately."
    }
  }

  stage('End-End Test') {
    isTestFailed=false;
		try {
			if (isUnix()) {
			sh "chmod +rwx e2eTest.sh"
			sh "./e2eTest.sh"
        
		} 
		else {
			bat(/start e2eTest.bat/)       
		}
		} catch (err) {
			isTestFailed = true;
		}
		//Collect Unit test reports to show in Jenkins
		junit 'output/junitresults.xml'
		if(isTestFailed){
			error "Tests Failed. Fix them immediately."
		}
	}
  

  stage('Contract Deploy to Test') {
    if (isUnix()) {
      sh "node_modules/.bin/truffle migrate --network live"
    } else {
      bat(/"${env.TRUFFLE_PATH}\\truffle" migrate/)
    }
  }
  
}
}
}
