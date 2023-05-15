pipeline {
    agent any
        stages {
        stage('Initialize'){
            steps{
                echo "Esta es el inicio"
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B package'
            
            }
        }
            
        stage('Test') {
            steps {
                 sh "mvn clean verify" 
            
            }
        }
       
            
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: "nexus2",
                            protocol: "http",
                            nexusUrl: "localhost:8081/repository/actividad6Maven/",
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: "http://localhost:8081/repository/actividad6group/",
                            credentialsId: "nexusCredential",
                            artifacts: [
                                [artifactId: pom.artifactId,
                                        classifier: '',
                                        file: artifactPath,
                                        type: pom.packaging]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        
            } 
     }
    
    post {
       always {
          junit(
        allowEmptyResults: true,
        testResults: '*/test-reports/.xml'
      )
      }
      failure{
	    slackSend( channel: "#fundamentos-de-devops", token: "ZPY4JL5GoNwggJhsmszYKDXN", color: "good", message: "${custom_msg()}")
	  }
   } 
}

def custom_msg()
	        {
	        def JENKINS_URL= "localhost:8080"
	        def JOB_NAME = env.JOB_NAME
	        def BUILD_ID= env.BUILD_ID
	        def JENKINS_LOG= " FAILED: Job [${env.JOB_NAME}] Logs path: ${JENKINS_URL}/job/${JOB_NAME}/${BUILD_ID}/consoleText"
	        return JENKINS_LOG
	    }
