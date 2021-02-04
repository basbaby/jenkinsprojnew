pipeline {
    agent any
    tools {
        maven "Maven" 
	  
	    
    }
   stages {
   	
       /*stage("upload to nexus") {
      steps {
        script {
	  LAST_STARTED = env.STAGE_NAME
          echo "hello";
          pom = readMavenPom file: "pom.xml";
          filesbyGlob = findFiles(glob: "target/*.jar");
          echo "${filesbyGlob[0].path}";
          nexusArtifactUploader(artifacts: [[artifactId: pom.artifactId, classifier: "", file: filesbyGlob[0].path, type: "jar"]], credentialsId: "nexus", groupId: pom.groupId, nexusUrl: "localhost:8081", nexusVersion: "nexus3", protocol: "http", repository: "com.testnjc", version: currentBuild.number.toString())
        }
      }
    }*/
	   
      stage("Build") {
      		steps {
	    		script {
				LAST_STARTED = env.STAGE_NAME
				container_Up = false
				configFileProvider([configFile(fileId: "706c4f0b-71dc-46f3-9542-b959e2d26ce7", variable: "settings")]){
				sh "mvn -f pom.xml -s $settings clean install -DskipTests "     
				}
		    	} 
            	}    
      } 
	   
     
	   
    //stage("Generate Reports") {
    //		steps {
//			script {
//				LAST_STARTED = env.STAGE_NAME
//				cucumber(failedFeaturesNumber: -1, failedScenariosNumber: -1, failedStepsNumber: -1, fileIncludePattern: "cucumber.json", jsonReportDirectory: "cucumber-API-Framework/target", pendingStepsNumber: -1, skippedStepsNumber: -1, sortingMethod: "ALPHABETICAL", undefinedStepsNumber: -1)
//			}
 //               }
 //   }
	   
	  
    stage("Deploy to Cloudhub"){
        	steps {
			script {
				LAST_STARTED = env.STAGE_NAME
				configFileProvider([configFile(fileId: "706c4f0b-71dc-46f3-9542-b959e2d26ce7", variable: "settings")]){
					
				sh "mvn -f pom.xml -s $settings package deploy -DmuleDeploy -DskipTests -Dkey=mymulesoft -Danypoint.username=anoop-rk -Danypoint.password=Anoop^1961 -DapplicationName=jenkinsprojnew-mule -Dcloudhub.region=us-east-2 -Danypoint.platform.client_id=2873c31e7152405e9dc38600007108e8 -Danypoint.platform.client_secret=70da4Da4228749B7A33C848E9a3C0849"
				}
			}
             	}
    }
	   
      
   
   }
   post {
        failure {
	    script {
	    		emailbody = "Build Failed at $LAST_STARTED Stage. Please find the attached logs for more details."
          		readProps= readProperties file: "cucumber-API-Framework/email.properties"
				emailext(subject: "$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!", body: "$emailbody", attachLog: true, from: "${readProps["email.from"]}", to: "${readProps["email.to"]}")
                        if (container_Up)  {
		 		sh "docker rm -f jenkinsprojnew"	
				sh "sleep 10"
				sh "docker rmi jenkinsprojnew:mule"
				
		 }
	    } 
        }
    } 
}
